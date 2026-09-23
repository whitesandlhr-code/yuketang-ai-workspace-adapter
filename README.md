# 长江雨课堂 AI Workspace 兼容适配

本项目记录对 ScriptCat 脚本「长江雨课堂全自动刷课 v1.0.6」的新版 AI 学习空间兼容性分析与适配方案。

## 原项目

- 原脚本：长江雨课堂全自动刷课
- 原作者：niunai
- 原始版本：v1.0.6
- 原始发布平台：ScriptCat
- 原始页面：https://scriptcat.org/zh-CN/script-show-page/5713

原脚本版权归原作者所有。

原项目未明确声明软件许可证，因此在取得原作者进一步授权之前，本仓库暂不重新发布完整原脚本或对其重新授权。

## 适配背景

新版长江雨课堂 AI Workspace 已不再使用旧版 `.nav-item-leaf-box .leaf-item` 课程目录结构。

因此旧脚本可能出现“扫描完成：共0个内容”“所有内容已完成”等错误判断。

## 已确认的新页面结构

新版 AI Workspace 基于 Vue 2。

调试确认页面运行时可获取：

- `allLeafInfo`：完整学习单元数据
- `progressInfo.leaf_schedules`：课程学习状态
- `formatLeafProgress()`：页面自身的完成度计算逻辑
- `clickLeaf()`：课程导航
- `route.params.leaf_id`：当前学习内容 ID
- `route.query.node_id`：当前目录节点 ID

## 主要适配内容

- 从旧 DOM 扫描迁移至 Vue 运行时课程数据
- 修复课程数量扫描为 0
- 修复课程完成状态判断
- 修复 SPA 页面切换后的 DOM 引用失效
- 使用 `allLeafInfo` 中完整的 `node_id`
- 使用页面自身 `clickLeaf()` 进行课程切换
- 使用 `leaf_id + node_id` 验证页面切换
- 保留原脚本的视频播放器控制逻辑

## 当前测试状态

目前已验证：

- [x] 正确读取课程目录
- [x] 正确识别视频和图文
- [x] 正确识别完成与未完成项目
- [x] 正确获取 `node_id`
- [x] 正常调用 `clickLeaf()` 切换课程
- [x] 视频页面能够正常播放
- [x] 静音和二倍速逻辑正常工作
- [ ] 图文 / PPT 页面兼容性继续测试
- [ ] 更多课程环境测试

## 技术结构

Vue 2 Runtime  
↓  
`allLeafInfo`  
↓  
`progressInfo.leaf_schedules`  
↓  
`formatLeafProgress()`  
↓  
未完成学习单元  
↓  
`clickLeaf(fullLeaf)`  
↓  
Vue Router  
↓  
视频 / 图文页面

## 免责声明

本项目为非官方兼容性研究与技术记录。

本项目与雨课堂、长江雨课堂以及原脚本作者不存在官方隶属关系。

请遵守所在学校、课程及平台的相关规定。
