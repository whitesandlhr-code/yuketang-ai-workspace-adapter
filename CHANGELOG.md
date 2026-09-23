# Changelog

## v1.1.0 - AI Workspace compatibility

### Fixed

- 修复新版 AI 学习空间课程扫描为 0 的问题
- 修复旧 `.leaf-item` DOM 结构失效
- 修复 SPA 页面切换后的 DOM 引用问题
- 修复课程节点定位
- 修复完成状态识别

### Changed

- 使用 Vue 2 运行时数据获取课程结构
- 使用 `allLeafInfo` 获取完整 leaf
- 使用 `progressInfo.leaf_schedules` 获取学习状态
- 使用 `formatLeafProgress()` 计算完成度
- 使用 `clickLeaf()` 进行课程导航
- 使用 `leaf_id + node_id` 验证路由切换

### Preserved

- 静音
- 二倍速
- 后台播放
- 防暂停
- 原脚本 UI 面板

### Current status

- 视频课程适配已验证
- 图文 / PPT 页面仍在继续测试
- 更多课程环境兼容性仍需验证
