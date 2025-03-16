# ros2 cost function

### Twirling

##### brief:防止机器人运动过程中旋转过大，惩罚角速度
```c++
double TwirlingCritic::scoreTrajectory(const dwb_msgs::msg::Trajectory2D & traj)
{
  return fabs(traj.velocity.theta);  // add cost for making the robot spin
}
}  // namespace dwb_critics
```

### Oscillation

##### brief:防止机器人只是前后移动，轨迹振荡
场景1：机器人向前移动一米，然后向后移动两毫米。另一个向前运动将被认为是振荡的，因为x维度将从正翻转到负，然后再翻转到负。因此，当对不同的轨迹进行评分时，正速度命令将得到oscillation_score（-5.0，或无效），只有负速度命令将被视为有效。

场景2：机器人向前移动一米，然后向后移动一米。因此，机器人在翻转x方向的符号后移动了一米，该符号大于我们的oscillation_reset_dist，因此它不被认为是振荡的，因此所有轨迹都被认为是有效的。

### RotateToGoal

##### brief:当机器人足够接近目标，才允许机器人旋转到目标方向
存在有三个不同的阶段。

1） 如果当前姿势在距离目标姿势的xy_goal_tolerance LINEAR距离之外，该评论家将只返回0.0分。

2） 如果在xy_goal_tolerance范围内，且机器人仍以非零线性运动移动，则该评论家将仅允许比当前速度慢的轨迹来停止机器人（在机器人的加速度限制内）。返回的分数将是机器人的线速度的平方，乘以slowing_factor参数（默认值5.0），再加上scoreRotation的结果。

3） 如果在xy_goal_tolerance范围内，且机器人的线性运动足够小，则该评论家会将线性运动的轨迹评分为无效，并根据scoreRotation方法的结果对其余轨迹进行评分

### PreferForward

##### brief:向前移动的轨迹得分会更高
有三种不同的评分条件：

1） 如果轨迹的x速度为负，则返回惩罚

2） 如果轨迹的x较低，θ也较低，则返回惩罚。

3） 否则，返回轨迹θ的缩放版本。

```c++
if (traj.velocity.x < 0.0) {
    return penalty_;
  }
  // strafing motions also bad on such a robot
  if (traj.velocity.x < strafe_x_ && fabs(traj.velocity.theta) < strafe_theta_) {
    return penalty_;
  }

  // the more we rotate, the less we progress forward
  return fabs(traj.velocity.theta) * theta_scale_;
 }
```

## BaseObstacleCritic

##### brief:基于路径经过cosmap的位置对轨迹进行评分
```c++
double score = 0.0;
for (unsigned int i = 0; i < traj.poses.size(); ++i) {
double pose_score = scorePose(traj.poses[i]);
score = static_cast<double>(sum_scores_) * score + pose_score;
}
```

## ObstacleFootprintCritic

##### brief:机器人足迹上的所有点都没有接触到costmap中的障碍物来对轨迹进行评分，防压脚
检查足迹的边界是否有与物体发生碰撞

### GoalDistCritic

##### brief:挑选接近目标位姿的轨迹
先将path根据地图分辨率进行插值，以path最靠近local cost map边界的点为目标，所在格子的cost为0，计算每个格子到目标的曼哈顿距离作为cost，相邻的格子cost依次加1。将每条轨迹经过的格子cost相加（或者取最后一个，或者相乘）

### GoalAlignCritic

##### brief:根据机器人是否指向目标点挑选相应的的轨迹
用每个轨迹采样点正前方一定距离的格子来计算cost，继承自GoalDistCritic

### PathDistCritic

##### brief:挑选离Path最近的轨迹
先将path根据地图分辨率进行插值，path所在的格子cost为0，往四周扩展，计算每个格子到目标的曼哈顿距离作为cost，相邻的格子cost依次加1。

### PathAlign

##### brief:挑选朝向Path的轨迹
用每个轨迹采样点正前方一定距离的格子来计算cost，继承自PathDistCritic