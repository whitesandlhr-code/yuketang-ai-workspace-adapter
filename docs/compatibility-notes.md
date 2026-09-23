# Compatibility Notes

## 旧版实现方式

原脚本主要通过 DOM 结构扫描课程内容，例如：

`.nav-item-leaf-box .leaf-item`

并通过：

- `.leaf-item-tag`
- `.leaf-item-title`
- `.leaf-item-status`
- `.is-active`

识别视频、图文、标题、完成状态和当前学习项。

新版 AI Workspace 更新后，这些旧 DOM 结构已经无法稳定使用，因此原脚本可能出现：

- 扫描课程数量为 0
- 错误判断“所有内容已完成”
- SPA 切换后保存的 DOM 引用失效
- 无法自动切换到下一学习单元

## 新版 AI Workspace

调试确认新版页面基于 Vue 2。

页面 DOM 节点上可以访问 Vue 实例：

`element.__vue__`

核心课程目录组件的运行时数据中可获取：

- `menuData`
- `allLeafInfo`
- `progressInfo`
- `clickLeaf`
- `formatLeafProgress`
- `route`

## menuData

`menuData` 用于保存课程章节和学习单元顺序。

课程结构大致为：

chapter  
↓  
section_leaf_list  
↓  
leaf_list

其中实际学习内容主要包括：

- `leaf_type = 0`：视频
- `leaf_type = 3`：图文

测试课程中可以从 `menuData` 获取 18 个实际学习内容。

## allLeafInfo

`allLeafInfo` 保存更加完整的学习单元信息。

其中一个重要区别是：

`menuData` 中部分 leaf 的 `node_id` 可能为 `0`。

而同一个 leaf 在 `allLeafInfo` 中具有真实的 `node_id`。

例如：

`menuData`

- `id = 48970548`
- `node_id = 0`

`allLeafInfo`

- `id = 48970548`
- `node_id = 8282740`

因此课程导航应优先使用 `allLeafInfo` 中的完整 leaf 对象。

## 完成状态

新版页面不再依赖旧 DOM 图标判断课程完成状态。

核心数据来自：

`progressInfo.leaf_schedules`

同时页面自身提供：

`formatLeafProgress(leaf)`

用于计算学习单元完成度。

实际测试中：

- 已完成项目可以得到 `100%`
- 未完成项目可以得到 `0%`

因此新版适配优先调用页面自身的：

`formatLeafProgress()`

而不是重新猜测完成状态。

## clickLeaf

新版页面提供：

`clickLeaf(leaf)`

用于课程导航。

调试发现，该函数内部会根据：

- `id`
- `node_id`

寻找对应学习单元。

因此传入的 leaf 应包含正确的 `node_id`。

这也是使用 `allLeafInfo` 而不是直接使用原始 `menuData` leaf 的主要原因之一。

## Vue Router

课程切换完成后，可以通过当前路由确认切换结果。

主要字段：

`route.params.leaf_id`

表示当前学习单元 ID。

`route.query.node_id`

表示当前目录节点 ID。

因此适配逻辑可以通过：

`leaf_id + node_id`

确认 SPA 页面是否已经成功切换。

## 新版适配流程

课程扫描：

`menuData`

↓

确定课程顺序

↓

使用 `id` 在 `allLeafInfo` 中寻找完整 leaf

↓

使用 `formatLeafProgress()` 判断完成状态

↓

建立未完成课程队列

↓

调用 `clickLeaf(fullLeaf)`

↓

通过 Vue Router 验证切换

↓

进入视频或图文页面

## 视频页面

原脚本的视频控制逻辑基本可以继续使用。

当前已验证：

- 能找到视频元素
- 能正常调用 `video.play()`
- 静音逻辑正常
- 二倍速逻辑正常
- 页面切换后能够继续初始化播放器

## 图文 / PPT

普通图文逻辑仍需要继续测试。

部分课程内容会调用 Office Online / PowerPoint Viewer。

目前观察到部分外部 Office Online 资源可能出现：

- 404
- ChunkLoadError
- `appChrome is not defined`

这些错误来自外部文档预览组件，并不一定代表课程导航逻辑失败。

因此图文 / PPT 类型仍属于后续兼容性测试重点。

## 当前适配状态

已验证：

- 课程目录读取
- 视频 / 图文类型识别
- 完成状态读取
- `node_id` 补全
- `clickLeaf()` 导航
- Vue Router 切换验证
- 视频播放
- 静音
- 二倍速

仍需继续测试：

- 图文课程
- PPT / Office Online 页面
- 不同学校课程结构
- 更多长江雨课堂课程版本

## 版权说明

本项目基于 ScriptCat 上「长江雨课堂全自动刷课 v1.0.6」进行兼容性研究。

原脚本版权归原作者所有。

由于原项目目前未明确声明软件许可证，在获得原作者进一步授权前，本仓库主要用于保存技术分析、兼容性说明和修改记录。
