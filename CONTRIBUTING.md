# 笔记维护约定

## 分类

每篇文章只放入一个主分类，按其核心问题归类，不按“安装”“命令”“运行”等文章类型另建顶层目录。

- `math/`：数学基础，包括线性代数、概率统计、数值优化。
- `robotics/`：SLAM 与机器人，包括视觉、激光与惯性融合、建图、规划、标定与评估。
- `ros/`：ROS 与中间件，包括 ROS/ROS2 指令、参数、消息、Launch 和通信机制。
- `cpp/`：C++ 开发，包括语言基础、容器、并发、设计模式、Eigen/Boost/OpenCV 等库。
- `engineering/`：开发工具与工程实践，包括 Git、CMake、Docker、Conda、测试、CI/CD 与编辑器工具。
- `linux/`：Linux 与嵌入式，包括 Shell、Ubuntu、Jetson/Xavier 系统配置、驱动与硬件接入。
- `qt/`：Qt 开发，包括界面编程、项目配置和部署。
- `misc/`：应用与杂记，包括暂未形成独立知识体系的软件使用笔记。

涉及多个领域时，以读者查找这篇文章的核心问题为准。例如 Jetson 上安装 Qt 归入 `qt/`，ROS 2 的 Docker CI/CD 归入 `engineering/`。

## 文件、图片与链接

- Markdown 顶部使用完整文章名作为一级标题；正文从二级标题开始。
- 不添加用于博客发布的 YAML front matter；博客同步脚本会生成元数据。
- 保留既有文章文件名，避免无必要的公开 URL 变化；迁移后的两个 Jetson 笔记分别是 `linux/Ubuntu-xavier.md` 与 `linux/Jetson常用命令.md`，内容用途不同，不合并覆盖。
- 图片统一放在根目录 `media/`；历史图片保留原目录命名空间，如 `media/Algorithm/`、`media/C++/`，不要把它们当成主分类。
- 新增图片可使用 `media/<文章文件名>/`，从分类中的笔记用 `../media/<文章文件名>/图片.png` 引用。
- 使用标准 Markdown 图片语法，避免依赖 Obsidian 专属的 `![[图片]]`；图片名称、路径区分大小写。
- 文件名包含括号或空格时，推荐 `[说明](<相对路径>)` 写法；普通文件链接也应使用相对路径。
- 移动文章或附件时，同时修正文章内部图片、其他笔记中的引用和 README 索引。不要直接全局替换正文、示例代码中的路径。
- README 的心得笔记索引只列出各分类中的 Markdown 文章，不添加“参考资料”分组或 PDF 附件列表。学习资源和软件说明保留在后续章节。

## 博客联动

本目录作为博客仓库的 `notes/` 子模块使用时，分类映射位于父仓库 `scripts/sync_notes.py`，与上述八个顶层目录一致。迁移历史和旧公开 URL 兼容映射位于父仓库 `data/note-migration.json`。

父仓库的 `content/notes/`、`static/notes/` 和搜索索引是生成结果，不在生成文件中直接维护内容。新增笔记后从博客根目录运行 `python3 scripts/sync_notes.py` 和 `hugo --minify`，并检查搜索、图片、PDF 和文章链接。

本次整理以本地源仓库提交 `7b7ddec` 为基线；删除的旧笔记不因分类整理而重新添加。提交、推送和发布需要用户明确授权。
