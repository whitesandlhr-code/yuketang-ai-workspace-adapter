# Patches

本目录用于记录针对原始脚本 v1.0.6 的兼容性修改说明。

当前仓库暂不重新发布完整原始脚本源码。

## AI Workspace Compatibility Patch

新版长江雨课堂 AI Workspace 更新后，原脚本依赖的课程 DOM 结构已经失效。

主要修改方向如下。

### 1. 课程扫描

旧版：

`.nav-item-leaf-box .leaf-item`

新版：

通过 Vue 2 运行时读取：

- `menuData`
- `allLeafInfo`

其中：

- `menuData` 用于确定课程顺序
- `allLeafInfo` 用于获得完整课程节点和真实 `node_id`

### 2. 完成状态判断

旧版通过 DOM 状态图标判断：

`.leaf-item-status`

新版使用：

`progressInfo.leaf_schedules`

并优先调用页面自身：

`formatLeafProgress(leaf)`

计算学习单元完成度。

### 3. 课程切换

旧版通过模拟点击 DOM 元素：

`simulateClick(content.element)`

新版调用页面自身：

`clickLeaf(fullLeaf)`

其中 `fullLeaf` 应来自 `allLeafInfo`。

### 4. 路由验证

课程切换后通过：

`route.params.leaf_id`

和：

`route.query.node_id`

确认当前页面是否已经切换到目标学习单元。

### 5. SPA DOM 引用

旧脚本会长期保存：

`content.element`

新版 AI Workspace 属于 SPA 页面，路由切换后旧 DOM 引用可能失效。

因此新版适配不再依赖长期保存的课程 DOM 节点。

### 6. 视频播放器

原视频播放逻辑大部分可以继续使用，包括：

- HTML5 `video`
- 静音
- 倍速
- 播放状态检测

新版主要增加页面切换后的播放器重新定位和初始化。

## 当前状态

已验证：

- 课程扫描
- 课程类型识别
- 完成状态判断
- `node_id` 映射
- `clickLeaf()` 导航
- 路由验证
- 视频播放

仍在测试：

- 普通图文内容
- PPT / Office Online 内容
- 不同课程环境
