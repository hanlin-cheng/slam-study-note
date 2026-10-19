# lightning-lm

## 一、重定位

### 1.核心判断条件

重定位的核心判断在 `Align()` 函数中：

```
// 文件：lidar_loc.cc:610
if (!loc_inited_) {
    // 进入重定位流程
}
```

| 序号  | 触发条件             | 判断逻辑                                   | 代码位置                  | 是否自动 |
| ----- | -------------------- | ------------------------------------------ | ------------------------- | -------- |
| **1** | **系统首次启动**     | `loc_inited_ = false` (初始状态)           | lidar_loc.cc:610          | 自动     |
| **2** | **手动设置初始位姿** | `SetInitialPose()` → `loc_inited_ = false` | lidar_loc.cc:561-568      | 手动     |
| **3** | **RViz2 重定位**     | `/initialpose` 话题 → `SetExternalPose()`  | unified_system.cc:775-800 | 手动     |
| **4** | **连续匹配失败**     | `match_fail_count_ ≥ 100`                  | lidar_loc.cc:834-843      | 被动标记 |
| **5** | **模式切换**         | SLAM → Localization                        | unified_system.cc         | 自动     |
| **6** | **外部位姿救援**     | `TryOtherSolution()`                       | lidar_loc.cc:472-494      | 条件触发 |

### 2.重定位完整流程

#### 1）**流程图总览**

```
┌─────────────────────────────────────────────────────────────┐
│                      定位主循环 Align()                       │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
              ┌────────────────┐
              │ loc_inited_?   │
              └────┬───────┬───┘
                   │       │
              NO   │       │   YES
                   │       │
                   ▼       ▼
          ┌────────────┐  ┌──────────────────┐
          │ 重定位流程  │  │ 正常定位流程      │
          └────────────┘  │ (NDT匹配 + 融合) │
                          └──────────────────┘
```

#### 2）**详细流程步骤**

##### **阶段 1: 重定位触发判断** (lidar_loc.cc:610-669)

```
// Step 1: 检查是否需要重定位
if (!loc_inited_) {
    SetInitRltState();  // 设置定位状态为 INITIALIZING
    
    // ═══════════════════════════════════════════════
    // 优先级 1: 手动设置的初始位姿（最高优先级）
    // ═══════════════════════════════════════════════
    if (initial_pose_set_) {
        if (InitWithFP(input, initial_pose_)) {
            initial_pose_set_ = false;
            return;  // ✅ 成功，退出
        }
    }
    
    // ═══════════════════════════════════════════════
    // 优先级 2: 功能点自动初始化
    // ═══════════════════════════════════════════════
    if (options_.init_with_fp_) {
        // 检查是否满足重试条件
        if (!fp_init_fail_pose_vec_.empty() && current_dr_pose_set_) {
            bool should_try = 
                (current_time - fp_last_tried_time_) > 2.0 ||      // 时间间隔 > 2s
                (position_diff > 0.3) ||                           // 移动距离 > 0.3m
                (rotation_diff > 10 * M_PI / 180.0);              // 旋转角度 > 10°
            
            if (!should_try) {
                return;  // ❌ 跳过本次尝试
            }
        }
        
        // 遍历所有功能点
        auto all_fps = map_->GetAllFP();
        for (const auto& fp : all_fps) {
            map_->LoadOnPose(fp.pose_);
            if (InitWithFP(input, fp.pose_)) {
                return;  // ✅ 成功，退出
            }
        }
        
        // ❌ 所有功能点都失败，记录失败历史
        fp_last_tried_time_ = current_time;
        fp_init_fail_pose_vec_.emplace_back(current_dr_pose_);
    }
    
    return;  // 初始化未成功，不执行后续定位流程
}
```

##### **重定位触发条件总结**:

| 条件类型        | 触发表达式                                   | 说明                      |
| --------------- | -------------------------------------------- | ------------------------- |
| 首次启动        | `loc_inited_ == false`                       | 系统初始状态              |
| 手动重定位      | `initial_pose_set_ == true`                  | 用户通过 RViz2 或代码设置 |
| 功能点重试-时间 | `(current_time - fp_last_tried_time_) > 2.0` | 距离上次失败超过2秒       |
| 功能点重试-距离 | `position_diff > 0.3`                        | 移动超过30cm              |
| 功能点重试-角度 | `rotation_diff > 10°`                        | 旋转超过10度              |

##### **阶段 2: InitWithFP() 执行** (lidar_loc.cc:418-462)

```cpp
bool LidarLoc::InitWithFP(CloudPtr input, const SE3& fp_pose) {
    // ═══════════════════════════════════════════════
    // Step 1: 执行 Yaw 角搜索
    // ═══════════════════════════════════════════════
    SE3 pose_esti = fp_pose;
    double fitness_score;
    CloudPtr output_cloud(new PointCloudType);
    
    loc_inited_ = YawSearch(pose_esti, fitness_score, input, output_cloud);
    
    if (loc_inited_) {
        // ✅ 重定位成功
        current_timestamp_ = math::ToSec(input->header.stamp);
        localization_result_.confidence_ = fitness_score;
        current_abs_pose_ = pose_esti;
        localization_result_.pose_ = pose_esti;
        localization_result_.timestamp_ = current_timestamp_;
        localization_result_.lidar_loc_valid_ = true;
        localization_result_.status_ = LocalizationStatus::GOOD;
        
        last_abs_pose_set_ = true;
        last_abs_pose_ = pose_esti;
        current_score_ = fitness_score;
        
        // 设置相对定位起点
        if (current_lo_pose_set_) {
            last_lo_pose_ = current_lo_pose_;
            last_lo_pose_set_ = true;
            last_dr_pose_ = current_dr_pose_;
            last_dr_pose_set_ = true;
        }
        
        // 清空失败记录
        fp_init_fail_pose_vec_.clear();
        
    } else {
        // ❌ 重定位失败
        LOG(INFO) << "init failed, score: " << fitness_score;
        fp_init_fail_pose_vec_.emplace_back(fp_pose);
        fp_last_tried_time_ = 1e-6 * input->header.stamp;
    }
    
    return loc_inited_;
}
```

##### **InitWithFP 关键步骤**:

1. 接收功能点位姿作为初值
2. 调用 `YawSearch()` 进行角度搜索
3. 根据置信度判断成功/失败
4. 更新定位状态和历史记录

##### **阶段 3: YawSearch() 角度搜索** (lidar_loc.cc:351-416)

```cpp
bool LidarLoc::YawSearch(SE3& pose, double& confidence, 
                         CloudPtr input, CloudPtr output) {
    // ═══════════════════════════════════════════════
    // Step 1: 准备搜索参数
    // ═══════════════════════════════════════════════
    PoseRPYD RPYXYZ = math::SE3ToRollPitchYaw(pose);
    double init_yaw = RPYXYZ.yaw;
    
    // 从配置读取
    double angle_search_step = 60;  // 60度步长
    double radius = 180.0;           // ±180度范围
    int step = std::round(2 * radius / angle_search_step);
    
    std::vector<double> searched_yaw(step);
    std::vector<double> scores(step);
    std::vector<SE3> pose_opti(step);
    
    // ═══════════════════════════════════════════════
    // Step 2: 粗分辨率搜索 (5.0m分辨率)
    // ═══════════════════════════════════════════════
    for (int i = 0; i < step; ++i) {
        double search_yaw = init_yaw + i * angle_search_step - radius;
        RPYXYZ.yaw = search_yaw;
        SE3 pose_esti = math::XYZRPYToSE3(RPYXYZ);
        
        // 使用粗分辨率 NDT 匹配
        Localize(pose_esti, scores[i], input, output, true);
        pose_opti[i] = pose_esti;
    }
    
    // 找到最佳匹配
    auto best_idx = std::max_element(scores.begin(), scores.end()) - scores.begin();
    confidence = scores[best_idx];
    pose = pose_opti[best_idx];
    
    // ═══════════════════════════════════════════════
    // Step 3: 高分辨率精配准 (1.0m分辨率)
    // ═══════════════════════════════════════════════
    if (confidence > options_.min_init_confidence_) {
        Localize(pose, confidence, input, output, false);
    }
    
    // ═══════════════════════════════════════════════
    // Step 4: 判断是否成功
    // ═══════════════════════════════════════════════
    bool yaw_search_success = false;
    if (confidence > options_.min_init_confidence_) {
        yaw_search_success = true;
        LOG(INFO) << "Yaw search success, score: " << confidence;
    }
    
    return yaw_search_success;
}
```

**YawSearch 搜索策略**:

| 阶段   | NDT分辨率 | 搜索范围       | 步长 | 目的             |
| ------ | --------- | -------------- | ---- | ---------------- |
| 粗搜索 | 5.0m      | ±180°          | 60°  | 快速找到大致方向 |
| 精配准 | 1.0m      | 粗搜索最佳结果 | -    | 精确优化位姿     |

**配置参数**:

```yaml
lidar_loc:
  min_init_confidence: 1.8        # 置信度阈值
  grid_search_angle_range: 180.0  # 搜索范围 ±180°
  grid_search_angle_step: 60      # 搜索步长 60°
```

##### **阶段 4: Localize() NDT 匹配** (lidar_loc.cc:929-1019)

```cpp
bool LidarLoc::Localize(SE3& pose, double& confidence, 
                        CloudPtr input, CloudPtr output, 
                        bool use_rough_res) {
    UL lock(match_mutex_);
    
    // ═══════════════════════════════════════════════
    // Step 1: 选择 NDT 分辨率
    // ═══════════════════════════════════════════════
    NDTType::Ptr ndt;
    if (use_rough_res && !loc_inited_) {
        ndt = pcl_ndt_rough_;  // 粗分辨率 5.0m
    } else {
        ndt = pcl_ndt_;         // 标准分辨率 1.0m
    }
    
    // ═══════════════════════════════════════════════
    // Step 2: 执行 NDT 配准
    // ═══════════════════════════════════════════════
    Eigen::Matrix4f guess_pose = pose.matrix().cast<float>();
    ndt->setInputSource(input);
    ndt->align(*output, guess_pose);
    
    // ═══════════════════════════════════════════════
    // Step 3: 获取结果
    // ═══════════════════════════════════════════════
    Eigen::Matrix4f trans = ndt->getFinalTransformation();
    confidence = ndt->getTransformationProbability();
    
    // ═══════════════════════════════════════════════
    // Step 4: 判断成功条件
    // ═══════════════════════════════════════════════
    bool loc_success = false;
    if (!loc_inited_ && confidence > options_.min_init_confidence_) {
        loc_success = true;  // 初始化阶段需要高置信度
    } else {
        loc_success = true;  // 已初始化后总是认为成功
    }
    
    // ═══════════════════════════════════════════════
    // Step 5: 可选的 ICP 精调整（仅初始化后）
    // ═══════════════════════════════════════════════
    if (options_.enable_icp_adjust_ && loc_inited_) {
        pcl_icp_->setInputSource(input_voxel);
        pcl_icp_->align(*output, trans);
        adjust_trans = pcl_icp_->getFinalTransformation();
        
        // 检查 ICP 结果是否合理
        if (position_diff <= 0.05 && angle_diff <= 0.05) {
            trans = adjust_trans;  // 采用 ICP 结果
        }
    }
    
    // 转换为 SE3
    pose = SE3(Quatd(rot), t_3d);
    
    return loc_success;
}
```

**Localize 关键点**:

- **粗分辨率**: 5.0m，用于快速搜索
- **精分辨率**: 1.0m，用于精确定位
- **ICP 调整**: 可选，进一步提升精度（仅初始化后）

### 3.重定位优先级体系

#### **优先级总览表**

| 优先级 | 重定位方式           | 触发条件                        | 执行时机     | 成功标准              | 代码位置             |
| ------ | -------------------- | ------------------------------- | ------------ | --------------------- | -------------------- |
| **P0** | **手动外部位姿**     | `initial_pose_set_ = true`      | 立即执行     | `confidence > 1.8`    | lidar_loc.cc:615-622 |
| **P1** | **功能点自动初始化** | `options_.init_with_fp_ = true` | 满足重试条件 | `confidence > 1.8`    | lidar_loc.cc:624-665 |
| **P2** | **外部位姿源救援**   | RTK/GPS 提供更优位姿            | 实时检查     | `score > 1.5×current` | lidar_loc.cc:472-494 |
| **P3** | **多源位姿验证**     | LO/DR/Self 差异大               | 每帧检查     | 分值最高者            | lidar_loc.cc:720-780 |
| **-**  | **失败标记**         | `match_fail_count_ ≥ 100`       | 被动状态     | 无自动恢复            | lidar_loc.cc:834-843 |

------

#### **优先级处理逻辑**

##### **1. P0: 手动外部位姿（最高优先级）**

```cpp
// lidar_loc.cc:615-622
if (initial_pose_set_) {
    if (InitWithFP(input, initial_pose_)) {
        LOG(INFO) << "init with external pose";
        initial_pose_set_ = false;
        return;  // 🛑 立即返回，不执行后续逻辑
    }
}
```

**特点**:

- ✅ **立即执行**，不受任何限制
- ✅ **一定会尝试**，即使刚刚失败过
- ✅ 用户主动触发，优先级最高
- ❌ 如果失败，会继续尝试 P1

**触发方式**:

```bash
# RViz2 中点击 "2D Pose Estimate"
# 或通过代码：
loc_->SetExternalPose(q, t);
```

------

##### **2. P1: 功能点自动初始化**

```cpp
// lidar_loc.cc:624-665
if (options_.init_with_fp_) {
    // 检查重试条件
    if (!fp_init_fail_pose_vec_.empty() && current_dr_pose_set_) {
        bool should_try = 
            (current_time - fp_last_tried_time_) > 2.0 ||  // ⏱️ 时间间隔
            (position_diff > 0.3) ||                       // 📏 移动距离
            (rotation_diff > 10°);                         // 🔄 旋转角度
        
        if (!should_try) {
            return;  // 🛑 跳过本次
        }
    }
    
    // 遍历所有功能点
    auto all_fps = map_->GetAllFP();
    for (const auto& fp : all_fps) {
        map_->LoadOnPose(fp.pose_);
        if (InitWithFP(input, fp.pose_)) {
            break;  // ✅ 成功，退出
        }
    }
}
```

**重试限制条件**（三者满足其一即可重试）:

| 限制类型 | 阈值   | 说明                | 目的             |
| -------- | ------ | ------------------- | ---------------- |
| 时间间隔 | > 2.0s | 距离上次失败超过2秒 | 避免频繁重试     |
| 移动距离 | > 0.3m | 机器人移动超过30cm  | 位置变化可能成功 |
| 旋转角度 | > 10°  | 机器人旋转超过10度  | 角度变化可能成功 |

------

##### **3. P2: 外部位姿源救援（RTK/GPS）**

```cpp
// lidar_loc.cc:472-494
bool LidarLoc::TryOtherSolution(CloudPtr input, SE3& pose) {
    double fitness_score;
    bool loc_success = Localize(pose, fitness_score, input, output);
    
    if (loc_success) {
        // 判断新位姿是否显著更优
        float score_th = std::min(1.5 * current_score_, current_score_ + 0.3);
        
        if (fitness_score > score_th && fitness_score > 1.0) {
            LOG(WARNING) << "External solution is better: " 
                        << fitness_score << " vs " << current_score_;
            pose = pose_esti;
            localization_result_.lidar_loc_smooth_flag_ = false;
            return true;  // ✅ 采用新位姿
        }
    }
    return false;
}
```

**判断标准**:

1. `fitness_score > max(1.5 × current_score_, current_score_ + 0.3)`
2. `fitness_score > 1.0`（绝对阈值）

------

##### **4. P3: 多源位姿验证（实时）**

```cpp
// lidar_loc.cc:720-780
SE3 guess_from_lo = last_abs_pose_ * delta_lo;    // LiDAR Odom
SE3 guess_from_dr = last_abs_pose_ * delta_dr;    // DR/轮速
SE3 guess_from_self = pred;                       // 自身外推

// 判断是否需要尝试 DR
bool try_dr = false;
if ((guess_from_dr - guess_from_lo).norm() >= 0.3 ||
    rotation_diff >= 0.5°) {
    try_dr = true;
}

// 判断是否需要尝试 Self
bool try_self = false;
if ((guess_from_self - guess_from_lo).norm() >= 0.3 &&
    (guess_from_dr - guess_from_self).norm() >= 0.3) {
    try_self = true;
}

// 同时尝试多个初值，选择最佳
loc_success_lo = Localize(guess_from_lo, score_lo, ...);
if (try_dr) {
    loc_success_dr = Localize(guess_from_dr, score_dr, ...);
    if (score_dr > score_lo - 0.1) {
        current_pose = guess_from_dr;  // 采用 DR
    }
}
if (try_self) {
    loc_success_self = Localize(guess_from_self, score_self, ...);
    if (score_self > fitness_score + 0.1) {
        current_pose = guess_from_self;  // 采用 Self
    }
}
```

**选择策略**:

- 默认使用 LO（LiDAR Odom）
- 如果 DR 分值更高 → 切换到 DR
- 如果 Self 分值最高 → 切换到 Self
- **退化检测**: 如果 LO 和 DR 收敛到不同位置但分值接近 → 判定退化，使用 DR

### 4.定位状态机

```
┌──────────┐
│   IDLE   │  (初始状态，loc_inited_ = false)
└────┬─────┘
     │ SetInitialPose() / 系统启动
     ▼
┌────────────────┐
│ INITIALIZING   │  (重定位中，match_fail_count_ = 0)
└────┬───────┬───┘
     │       │
     │ 成功  │ 失败
     ▼       ▼
┌────────┐  继续尝试...
│  GOOD  │  
└────┬───┘
     │ 连续匹配失败
     ▼
┌────────────────┐
│ FOLLOWING_DR   │  (100 ≤ match_fail_count_ < 300)
└────┬───────────┘
     │ 持续失败
     ▼
┌──────┐
│ FAIL │  (match_fail_count_ ≥ 300)
└──────┘
     │ 需要手动重定位
     ▼
   回到 INITIALIZING
```

**状态定义**:

| 状态           | 枚举值 | match_fail_count | 行为     | 输出位姿来源 |
| -------------- | ------ | ---------------- | -------- | ------------ |
| `IDLE`         | 0      | -                | 无定位   | 无           |
| `INITIALIZING` | 1      | 0                | 重定位中 | 无效         |
| `GOOD`         | 2      | < 100            | 正常定位 | NDT 匹配     |
| `FOLLOWING_DR` | 3      | 100-299          | 降级定位 | DR 递推      |
| `FAIL`         | 4      | ≥ 300            | 定位失效 | 无效         |

### 5.成功判定标准 

#### **1）重定位成功标准**

| 阶段         | 判断条件                            | 阈值 | 说明                 |
| ------------ | ----------------------------------- | ---- | -------------------- |
| **粗搜索**   | `confidence > min_init_confidence_` | 1.8  | Yaw 角搜索分值       |
| **精配准**   | `confidence > min_init_confidence_` | 1.8  | 高分辨率 NDT 分值    |
| **状态更新** | 置信度 + 状态标志                   | -    | `loc_inited_ = true` |

#### **2）分值计算方式**

```cpp
confidence = ndt->getTransformationProbability();
```

**NDT 置信度计算**:

- 基于点到分布的概率
- 值域: [0, +∞)
- 越大越好
- 典型好值: > 1.8

#### **3）失败处理策略**

| 失败次数 | 动作           | 状态           |
| -------- | -------------- | -------------- |
| 1-99     | 继续使用 NDT   | `GOOD`         |
| 100-299  | 切换到 DR 递推 | `FOLLOWING_DR` |
| ≥ 300    | 标记失效       | `FAIL`         |