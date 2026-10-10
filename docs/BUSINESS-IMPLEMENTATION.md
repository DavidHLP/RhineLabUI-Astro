# 博客业务整改实施与验收

规范：[BUSINESS-AUDIT.md](BUSINESS-AUDIT.md)。基线：`b797a4c60f3ff5f8bbd560e10f5de66cbabc1ca1`。

本文件记录实现和交付状态，原审查文档保留历史判断，不将源码修改写成运行验证通过。用户已授权按 implement-spec 实现整份规范。五列、每列至少一篇、稳定编号与地址、Three.js 原画质及交互参数、资源许可与署名保持原约束；不新增 CMS、账号、评论或定时发布。

## 任务依赖与当前证据

| 任务 | 覆盖 | 依赖 | 实现证据 | 状态 |
| --- | --- | --- | --- | --- |
| T1 发行与安装身份 | B02、B06 | 无 | build-pwa、package-cloudflare、manifest endpoint、CI | 源码已修改；远端包装未验证 |
| T2 正文交付与全文检索 | B04 | T1 保证新资源发行 | Terminal 当前正文、blog-search endpoint、客户端加载与缓存 | 源码已修改；规模、离线与异步行为待验证 |
| T3 阅读恢复与章节 | B01、B03 | T2 | view=static、启动失败入口、容器内章节定位 | 源码已修改；键盘与浏览器复现待验证 |
| T4 运营信息和收藏 | B05、B06、B07、B08 | T2 元数据契约 | 日期、近期排序、RSS、有效收藏计数、保存反馈、作者及日期同步 | 源码已修改；运行待验证 |
| T5 内容回归与验收 | B09 | T1–T4 | 扩展现有 check-content，包括资源发行夹具、构建产物与客户端逻辑 | 检查已编写；未运行 |
| T6 分支、PR、CI与合并 | 整份规范 | T5 正式验证 | 尚无提交、PR、CI结果 | 环境阻塞 |

实施子任务按互不重叠的文件所有权协作。implement-spec 原要求独立 worktree 和合并代理，但当前 `.git` 只读，创建任务分支失败；没有改写历史、移动已有工作区或绕过权限。

## 验收清单

| 条目 | 应取得的证据 | 本次状态 |
| --- | --- | --- |
| npm run build | remote-dev 对目标代码的 Astro 类型检查与构建成功 | Not Run |
| npm run check:content | 全部现有及新增检查通过；构建产物检查不得跳过 | Not Run |
| npm run check:viewport | 现有布局、构图和渲染尺寸计算通过 | Not Run |
| npm run build:cloudflare | 完整发行，许可校验及大小限制通过 | Not Run |
| 失败阅读 | WebGL/模型/启动字体失败后可通过键盘进入静态正文与重连；Novecento 单独失败继续回退 | Not Run |
| 纯阅读导航 | 首页/文章在 view=static 保持可读，返回交互有效，无 JS 静态仍可读 | Not Run |
| 正文异步 | 快速切换 A/B，较晚响应不覆盖当前文章；失败重试有效；离线可读取已缓存文章 | Not Run |
| 章节 | 直接深链接、正文内链接、前进后退及加载后定位指向可见正文 | Not Run |
| 长文图表 | 标题、内部链接、图片、附件、代码、表格在独立页及交互正文一致 | Not Run |
| 内容增长 | 对比少量和较多长文 HTML/离线总量，确认没有每页重复全集正文；全文检索不退化 | Not Run |
| 运营信息 | 两种正文日期一致，最近发布排序、RSS、安装身份及切换后的作者/日期正确 | Not Run |
| 收藏 | 撤稿编号保留且不计有效数量；存储不可写时明确临时状态，刷新行为符合提示 | Not Run |
| Chromium 模拟 | 手机尺寸布局、键盘与章节滚动，明确是模拟结果 | Not Run |
| 真实 iPhone | Safari 阅读、主屏幕安装及离线验证，单独记录设备与系统 | Not Run |
| GitHub 交付 | 单分支提交、PR、当前 head CI 与审查通过，再按项目规则合并 | 未创建 |

## 当前环境与审查

2026-10-10：`git switch -c codex/business-audit-implementation` 因 `.git` 只读失败；GitHub API 无法连接。默认 SSH 受系统配置文件权限错误阻断；指定已有用户 SSH 配置后仍报目标主机 DNS 解析失败。未关闭主机校验或修改系统配置，也未在本机运行项目构建、测试或服务。

项目图谱的结构查询与覆盖检查在当前受限策略下返回需要 approval、但 approval policy 为 never。已确认项目名称，后续以当前源码与调用位置补证，不把旧索引视为新代码的覆盖证据。

code-review 进行独立 Standards 与 Spec 两轴审查，另加跨模块集成审查。三位独立 subagent 已复查最终源码：Standards、Spec 和集成审查均未发现剩余必须修改项。此前指出的启动错误页焦点陷阱、失败入口等待其他请求及静态模式字体样式边界已修正；新增 StartupGate 检查所需的 Node 类型擦除语法兼容问题也已修正。该结论只代表源码审查，没有使用静态审查结果代替运行验收。

`git diff --check` 已通过。新增夹具检查长文 HTML 与资源发行保留，不证明 Astro Markdown 实际渲染、浏览器滚动或真实设备行为；B09 的运行验收仍未完成。

上一轮连续三次确认 remote-dev DNS 与 `.git` 只读阻塞，故当时未完成正式验证与交付。2026-10-10 接续执行时，已重新确认 `.git` 可写、remote-dev SSH 可用且远端工作区干净，创建 `codex/business-audit-implementation` 分支；远端 Node 为 v22.23.3。以下验证和交付记录将以本轮实际结果更新，不沿用历史通过结论。GitHub 仓库关闭了 issues，因此以本文 T1–T6 作为任务图，不创建或自动关闭不存在的 issue。
