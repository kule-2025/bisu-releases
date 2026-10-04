# 笔溯 BISU — 网文新手作者专属桌面创作工具

## 核心标签

`#网文创作桌面助手` `#爆款小说创作桌面助手` `#爆款网文创作桌面助手` `#网文写作工具` `#桌面写作软件`

> **当前代码版本：0.10.20**（2026-10-04；`package.json` / `Cargo.toml` / `tauri.conf.json` 三处同步；安装包随发布管线双源推送，线上版本以 latest.json 为准）

> 100% 本地离线 · 加密 SQLite · Tauri 2.1 + Rust + React 18 + TypeScript 5.4
> 不含任何云依赖 · 不收集任何用户数据 · 源码 100% 原创
> **AI 调用走用户自接 API Key**（DeepSeek / 智谱 / 硅基流动 / Ollama 等），Key 经 Argon2id 派生 + AES-256-GCM 本地加密，正文与设定不上传 BISU 任何服务器。

## 爆款智能体蜂群（多题材人格化创作流水线）

> 一个项目一组各有人格的智能体，围绕同一本书共享世界设定（项目级 RAG 注入），顺序分工产出可直接发布的章节初稿。入口：左侧「爆款蜂群」面板（`SwarmStudio.tsx`）。

### 7 角色流水线
选题策划 → 人设主笔 → 章节扩写 → 爽点节奏评审 → 质检 → 去 AI 味润色 → 平台合规审校。每步真实调用 LLM，扩写步后接 `quality_gate` 评分（< 60 自动重写一次），去 AI 味步接 `deai_humanize` 真实润色。

### 关键能力
- **模板库**：玄幻/都市/言情/悬疑/历史/科幻 6 题材各 2 个内置起步模板（手写 system_prompt），用户可新建/编辑/另存为自己的模板/停用；发起 run 时可选模板作为 persona 初始 prompt 注入。内置模板不可删，只能停用或另存。
- **项目级 RAG 真隔离**：`kb_docs` 带 `project_id`，蜂群世界设定只检索当前项目导入的知识，跨项目不串味。
- **token 预算熔断**：单 run 真实累计 prompt+completion token，超 80k 自动熔断。
- **断点续跑**：v36 checkpoint 记录进度，崩溃/中止后再次启动自动跳过已完成步、从未完成步续跑。
- **pro 门槛**：`create_swarm_run` 前置专业版校验（与知识库 RAG 门槛一致）。
- **多模态配图（默认关）**：仅当在「设置 → 图像生成」配置了图像模型时，step 卡片才出现「生成配图」按钮；异步文生图落本地 png，失败只提示不阻塞文本流水线。

### 使用方式
1. 在蜂群面板确认项目下 7 个智能体已就位（首次自动幂等灌入种子人格）。
2. 填章节号（可选）、本章需求/一句话钩子；下拉可选一个起步模板。
3. 点「发起蜂群 run」：后端立即返回 run_id 并后台跑流水线，**step 进度经 Tauri event 实时推送**（前端监听 `swarm_step` / `swarm_done`，不再 2s 轮询），无需手动刷新。
4. 过程中可点「取消」中止（置中止位，下一轮检查即退出）。
5. 失败/超预算后再次「发起蜂群 run」即从 checkpoint 断点续跑；单 run token 预算 80k 封顶防失控。
6. 配置了图像模型的 step 可点「生成配图」，缩略图直接在 step 卡片内展示。

## 下载安装

| 源 | 地址 |
|------|------|
| **GitHub 主源（推荐）** | https://github.com/kule-2025/bisu-releases/releases/download/v0.10.20/BISU_0.10.20_x64-setup.exe |
| **Gitee 备源（国内直连）** | https://gitee.com/king2030/bisu/releases/download/v0.10.20/BISU_0.10.20_x64-setup.exe |
| **更新元数据（主源）** | https://raw.githubusercontent.com/kule-2025/bisu-releases/main/latest.json （备源：`gitee.com/king2030/bisu/raw/master/latest.json`） |

> 下载后双击安装即可。Windows 10/11 x64，无需额外运行时依赖。
> **部署策略（2026-08-16，v2.0 / R13 拓扑反转）**：双源 = **GitHub 公共仓主源**（`bisu-releases`：根 latest.json（主端点，url 指向本仓 `v<ver>/` 归档）+ 全版本归档 + tags）+ **Gitee 公共仓备源**（`king2030/bisu` raw/master：latest.json + 当前版 exe/sig，**产物由发布管线从主源字节级同步**，sha256 一致性门禁）。客户端端点顺序：GitHub 主源 → Gitee CDN → Gitee raw 备源。发布通道为**本地构建自动部署**（`tools/publish_ssh.py` SSH 零凭据，主源先行 → 备源同步），GitHub Actions 已停用（零成本硬约束）。完整规范见 `docs/dual-source-deploy-sop/双源部署标准化流程文档.md`。

### 历史更新记录

- **品牌升级 v2.0**：新钢笔橙 Logo（`#EF6014`）与全套图标已随 v0.4.21 上线，启动画面 / 关于页视觉将呈现新品牌形象。
- **部署状态**：v0.4.21 双源部署完成后，本「已知问题」区将同步更新；历史 v0.4.20 的 Release 认证与 Gitee 同步问题已随本轮部署一并解决。

## v0.10.20 更新记录（2026-10-04）

> 本版聚焦 v0.10.19 四维度审查遗留的 15 项问题全量深度修复：统一发布子系统前端切换、四套 AI 聊天面板统一内核、6 张死表清理、latest.json 自动生成、Gitee token 安全加固、30 个废弃命令函数体删除、API 层职责梳理、构建配置可移植化、134 份历史文档归档。数据库 schema 升级至 v59。

### 核心修复与增强
- **统一发布子系统前端切换（P1）**：前端 API 层从旧 DEPRECATED 命令切换到 4 个统一命令（`publish_get_platforms`/`publish_execute`/`publish_get_records`/`publish_status_sync`），新建 `src/api/unifiedPublish.ts`，`MultiPlatformPublish` 核心流程适配统一命令，消除双轨技术债。
- **四套 AI 聊天面板统一内核（P1）**：新建公共组件 `AIChatQuotaBadge.tsx`（自包含额度拉取）和 `useUnifiedChat.ts`（额度预检+选模闸门+成功刷新），AIChatPanel 新增流式打字机+额度管控（原缺失），CustomAIPanel/AgentChat 统一使用公共额度徽章，四套入口行为一致。
- **6 张死表 DROP（P2）**：schema v59 迁移中 DROP `t_cloud_accounts`/`t_ai_offline_cache`/`long_serial_chapters`/`t_style_fingerprints`/`quality_reports`/`demo_note`（均零业务读写），减少 DB 体积。
- **latest.json 自动生成（P2）**：`build_release.py` 集成 latest.json 自动生成（版本号+下载URL+sha256）并上传双源，自动更新器正常工作。
- **Gitee token 传参加固（P2）**：`build_release.py` 将 access_token 从 URL query 改为 `Authorization: token` 请求头，日志脱敏（仅显示前4位+***），降低 token 泄漏风险。
- **30 个 DEPRECATED 命令函数体删除（P2）**：已摘除注册且前端零调用的废弃命令，删除函数体源码，减少代码量。
- **API 层职责梳理（P2）**：`publish.ts` 中 `qualityGateAnalyze` 迁移到 `qualityGate.ts`，消除 API 层职责模糊。
- **构建配置可移植化（P3）**：`build.config.json`/`build.ps1`/`build_and_deploy.py` 中机器级路径（D:\Rust/D:\NodeJS）改为环境变量优先+自动探测，换机器可直接构建。
- **134 份历史文档归档（P3）**：`docs/` 下历史版本文档归档到 `docs/archive/`，减轻维护负担。
- **Page 类型清理（P3）**：移除 `useBisuAppState.ts` 中 "more"/"projects" 无用类型。

### 数据库
- **schema v59**：DROP 7 张死表（含 `t_ai_usage`），CURRENT_VERSION 58→59。

### 回归验证
- `cargo check`：0 error
- `tsc --noEmit`：0 error
- 命令注册检查：PASS
- 构建配置路径检查：无硬编码绝对路径

## v0.10.19 更新记录（2026-10-04）

> 本版聚焦线上稳定性与安全清理：修复 1 项 P0 额度管控断裂、多处崩溃与体验问题，清理死代码与明文密钥备份，数据库 schema 升级至 v58。

### 修复
- **AI 额度管控列名断裂导致免费用户对话失败（P0）**：`usage_logs` 列名与 schema 不对齐，免费用户发起对话即报错。
- **变现仪表盘在旧库缺少表时崩溃**：未升级到最新 schema 的旧数据库打开变现仪表盘直接 panic。
- **蜂群导演台侧栏缺少付费锁标**：付费功能入口未显示付费锁标识。
- **窗口标题乱码**：应用窗口标题中文显示为乱码。

### 新增
- **AI 聊天页额度剩余次数显示**：聊天页实时展示当前套餐剩余可用次数。
- **数据看板 / 任务队列导航入口**：侧栏新增两个直达入口。

### 优化与清理
- **全局提醒轮询失败退避**：轮询失败后指数退避，避免无意义重试。
- **清理 5 个死代码文件 / 组件**。
- **安全**：归档含明文密钥的配置备份文件，建议在对应平台轮换密钥（详见根目录 `SECURITY_NOTES.md`）。

### 数据库
- **schema v58**：`usage_logs` 列名对齐。

## v0.10.17 更新记录（2026-10-03）

> 本版聚焦全功能端到端审查发现的 **P0 级功能性缺陷修复**，基于 v0.10.16 四维度审查（功能设计深度/业务闭环完整性/用户需求匹配度/改进方向），共修复 **4 项核心问题**，全量 cargo check + tsc 零错误通过。

### P0 核心修复（4项）
- **AI 模型配置双表密钥同步**：新架构面板（`t_ai_model_configs` + `t_secrets`）配置的模型，写作主引擎（`llm_chat` 只读 `llm_profiles`）完全看不到。修复：`ai_add_model`/`ai_update_model`/`ai_delete_model` 同步镜像到 `llm_profiles`（api_key 用旧 atrest 体系加密），两条链路共享同一份密钥，用户在任一面板配好后写作立即可用（`ai/mod.rs`）。
- **成长阶段查错表名**：`growth_get_stage` 查询 `publish_records`（旧表，无写入路径）判断"是否已发布"，实际发布记录写入 `mp_publish_records`，导致成长阶段永远停留在"未发布"。修复：改为查 `mp_publish_records`（`growth.rs:420`）。
- **AI 用量统计死表修复**：`ai_get_usage_summary` 读 `t_ai_usage`（前端零调用 `record_usage`，恒为 0），新面板"本月用量"永远显示 0。修复：改读真实落库的 `usage_log`（`llm_chat` 路径经 `log_llm_usage` 写入），按调用类型分组展示（`ai/mod.rs:411`）。
- **侧栏"爆款公式"重定向残留**：侧栏入口指向 `hitformula`（历史合并重定向页），用户点击后静默跳转无提示。修复：直接指向 `formulaengine`（`AppSidebar.tsx:76`）。

### 回归验证
- `cargo check`：0 error，54 warning（均为历史死代码/未使用变量警告）
- `tsc --noEmit`：0 error
- 前端 `vite build`：成功，118 个业务 chunk 混淆
- Tauri release + NSIS 打包：成功，安装包 40.6 MB
- 双源部署：GitHub + Gitee Release 均创建成功（HTTP 201），latest.json 双源一致性校验 PASS

### 审查遗留问题（后续版本处理）
- 付费转化闭环：支付为沙箱模式，升级弹层无在线购买按钮（需真实商户号接入）
- AI 聊天额度管控：`usage_track` 前端未调用，主聊天框不限额（需接入 AIChatPanel）
- 发布数据回流：6 平台适配器 `get_book_stats` 均返回 NotSupported（需平台开放 API）
- 三套新手引导串联冗余、两套漫画体系并存、发布中心双 Tab 重叠等中低优先级问题

## v0.10.16 更新记录（2026-10-02）

> 本版聚焦 v0.10.15 两轮深度修复的正式发布，共修复 **58 项问题**（P0×6 + P1×12 + P2×25 + P3×16），新增 **v56 数据库迁移**（12 张表纳入迁移版本管理），全量 tsc + cargo check 零错误通过。

### P0 编译阻断与数据一致性（6项）
- DataDashboard.tsx 非法 style2 属性移除
- TaskQueuePage.tsx 4 处非法 IconName（pause/play/alert×2）替换
- TaskQueuePage.tsx unknown 类型参数 String() 包裹
- 时间线删除事件复活修复：timeline_sync.rs 表为权威 + TimelineViewer.tsx 双保险

### P1 核心业务闭环（12项）
- 大纲 AI 迭代产物落地（applyAiVersionToOutline + 应用按钮）
- 大纲章节生成行为统一（两视图走同一 handleGenerateChapter）
- 世界观模板应用 toast 修复（wb.toast()→wb.showToast()）
- 剧本工坊编辑持久化（runDeai/resolveSwap 后 scriptSceneSave 落库）
- 条漫工坊分镜卡重复膨胀修复（INSERT 前 DELETE 旧卡）
- 改编规划草稿决策回灌（getPlan 补 keptIds/droppedIds/droppedOffIds）
- 短篇合集增量保存（persistCollection 单合集写库）
- 双条漫体系定位标注
- bisu:projects-updated 事件补监听
- v56 迁移：12 张表纳入 migrations.rs，CURRENT_VERSION 55→56
- 类型化 IPC 层 / 发布流命令死代码标注

### P2 体验与数据一致性（25项）
- 今日任务 hasWrittenToday 改用 growthTodayWords
- 创作目标编辑/停用/删除联动 reminder
- 双连续打卡体系同步（打卡回写 writing_daily_metrics）
- 角色 relationships 编辑抽屉新增角色关系 Tab
- 角色规则检查 Promise.all 全量并发（不再 20 角色截断）
- 长篇连载非正文操作增量命令（update_meta/update_volumes）
- 大纲版本恢复 4N 次 IPC → outline_replace 批量命令（1 次）
- 角色加载 N+1 → character_get_mentions_batch 批量命令
- 质检 issue 明细入 DB（config_kv 镜像 + hydrate 恢复）
- 其余 16 项体验优化与文案诚实化

### P3 优化与清理（16项）
- 工作台文件列表窗口化渲染（默认 20 个 + 展开折叠）
- 知识库/网文术语 alert 改 toast
- 6 个死 API 文件加"已废弃，零引用"标注
- 短篇合集新增编辑能力（改名/主题/简介/作者）
- 其余 12 项清理与标注

### 性能优化
- 大纲版本恢复：4N 次 IPC → 1 次批量 upsert
- 角色加载：N 次单卡 IPC → 1 次批量查询
- 今日任务：双路全量拉取 → 单次 task_list 客户端派生
- 长篇连载非正文操作：整 blob 重写 → 增量命令
- 短篇合集：全量遍历更新 → 单合集写库

## v0.10.6 新功能概览（2026-09-27）

> 本版在 v0.10.6 基础上聚焦 **D 批次全量深度修复**——**metrics 漏斗事件接线**、**视频分镜级产物落库（v54 迁移）**、**镜级精修回路五态状态机**、**统一轮询管理器（性能优化）**与**仓库卫生清理**五大方向，共 12 个文件变更（+652 / -8 行）。全部为真实代码修改。

### 一、L-1 变现双库孤岛收敛（核心）

- **统一收入汇总命令**：新增 `monetization_unified_summary`，同时聚合网文平台收入（monetization_records）和 IP 改编收入（income_ledger），返回全口径总收入，解决双库孤岛问题。
- **变现看板统一收入总览**：MonetizationDashboard 新增四卡片（网文收入 / IP收入 / 总收入 / 台账入口），随作品筛选实时刷新。

### 二、AI 密钥双表回退

- **get_secret 自动回退**：t_secrets 为空时自动回退 llm_profiles 旧表解密，避免预配置模型"未配置"误判。
- **模型测试端点智能适配**：图像模型走 /images/generations，视频模型走 /videos/generations，文本走 /chat/completions。

### 三、长篇连载免费开放 + 侧边栏重构

- **长篇连载免费**：从付费锁定集合移除，基础写作功能免费开放。
- **空间列表全页面可见**：任意功能页均可切换项目，未选中时展示全部项目列表。

## v0.10.5 新功能概览（2026-09-27）

> 本版在 v0.9.1 四方向 19 项深度增强之上，聚焦 **API 配置丢失四级防护**、**七类问题全面修复（21/28）**、**侧边栏导航重构**、**性能优化**与**支付订单管理补全**五大方向，共 179 个文件变更（+17962 / -20732 行）。全部为真实代码修改。

### 一、API 配置丢失四级防护

- **派生密钥进程内缓存（B01/B02）**：API Key 加解密所用的 Argon2id 派生对称密钥（32 字节）首次计算后缓存于进程内，按 salt 指纹校验自动失效重算；多线程并发只算一次，消除 30s 轮询 × N profile 导致的周期性 CPU 尖峰。缓存仅存派生密钥，绝不缓存解密后的明文 API Key。
- **解密失败 WARN 降频（B01）**：API Key 密文解密失败日志由每次调用即记降为每 60 秒至多一条，消除轮询连发噪音（实测每批 4 连发）。
- **加密错误向上传播（B-18）**：`encrypt_api_key` 在底层加密失败时直接返回 Err 由调用方 `?` 传播，不再静默落盘伪密文。
- **明文 Key 不落盘**：解密后的明文 API Key 仅按请求临时解密、随栈帧释放，不在内存中长期驻留。

### 二、七类问题全面修复（21/28）

- **P0 窗口位置死循环修复**：窗口隐藏到托盘后位于 (-32000,-32000)，旧越界钳制逻辑判定越界即 `set_position(0,0)`，但隐藏态下程序化 set_position 不改变可见几何，OS 仍上报 (-32000)，形成约 1586 次/秒的钳制死循环致 UI 线程单核 100% 永久卡死。修复：窗口不可见/最小化时跳过钳制、命中托盘隐藏标记坐标时跳过、记录程序化移动目标坐标用于回声消费，从事件序列上切断循环环。
- **M-01 版本时光机恢复名存实亡修复**：此前恢复只派发 `bisu:version-restored` 事件但全 src 无监听者，后端 restore 也只翻 `is_current` 不写盘，点了恢复界面与磁盘都不变。修复为前端优先：直接用 `replaceEditorContent` 写进编辑器缓冲后调用 `saveFile` 落盘，事件保留仅作通知。
- **M-12 高级编辑器 Ctrl+S 双写覆盖修复**：高级编辑器（ChapterEditor）激活时内部持有独立 content 缓冲并经 `chapterAutoSave` 落盘，全局 `saveFile` 写的是全局 `editContent` 旧快照，二者双写以旧快照覆盖新内容。修复：`useAdvancedEditor` 激活时全局 Ctrl+S 让行，只走 ChapterEditor 自身保存链路。
- **M-40 三层引导叠加弹窗修复**：首启时 Onboarding 完成后进入 OnboardingFlow，其 onDone 置 `onboardingDone` 为真；而 `guideOpen` 初值首启为 true，OnboardingFlow 刚结束同一渲染周期四步引导立刻叠加弹出。修复：`onboardingDone` 就绪后延迟 5 秒再放开四步引导，让用户先进入主界面。
- **P2 启动日志去噪**：账号 schema 迁移中对已存在列重跑 ALTER TABLE 产生 duplicate column WARN（约 6 列 × 2 次），改为先 `PRAGMA table_info` 检查列是否存在，存在则跳过，真实错误仍向上传播。
- **账号命令双轨退役计划**：旧命令集（register_account/login_account 等）标注 `@deprecated`，前端生产路径统一走新命令（account_register/account_login 等），旧命令保留供历史版本兼容，禁止在旧命令中新增业务逻辑。
- **其余修复**：涵盖主.rs BOM 头规范化、配置文件版本号同步、构建脚本路径一致性等多项工程修复。

### 三、侧边栏导航重构

- **52 项静态导航配置**：侧边栏导航项由每次渲染重建数组/对象升级为模块级静态 `SIDEBAR_ITEMS_BASE` 常量（52 项），消除每渲染重建开销。
- **七大功能分组**：导航按 ①创作（工作台/长篇/短篇/任务/目标/打卡）、②设定（世界观/角色/大纲/时间线/术语/记忆/知识库/风格）、③AI生产（AI创作/灵感/智能体/技能/蜂群/封面/大师/仿写）、④IP改编链（驾驶舱/改编规划/流水线/剧本/条漫/视频/成片/导演台）、⑤质检（去AI味/质量门控/逻辑扫描/体检/消痕报告/读者模拟/教练/评估）、⑥市场（排行榜/对标/爆款公式/大作/统计）、⑦发布与收益（发布/作品/素材/品牌/台账/看板）重组，经典旧版折叠为子组。
- **付费锁标识与标签**：付费功能项带 🔒 标识，聚合分析页带标签，开发者后台独立配置。

### 四、性能优化

- **API Key 派生密钥缓存**：消除每次解密都重算 Argon2id（64MB 内存 / 3 轮 / 4 并行，单次数十至数百 ms）的周期性 CPU 尖峰。
- **日志降频**：解密失败 WARN 60 秒节流，消除轮询日志刷爆。
- **静态导航配置**：52 项侧边栏数据模块级固化，不再每渲染重建。
- **锁序优化**：派生密钥缓存锁与 DB 锁分离，先在缓存锁之外取 salt，杜绝「派生锁 → DB 锁」与其他路径反向持锁的死锁序。

### 五、支付与订单管理补全

- **订单列表查询（list_orders）**：新增后端命令 `list_orders`，按用户查询历史订单（订单号/档位/周期/金额/状态/网关/创建时间/支付时间），默认 50 条、上限 200 条，金额同时返回分单位与元单位（两位小数）。设置页新增订单列表区块，前端 `listOrders()` 封装与 `OrderItem` 类型。
- **支付双轨架构**：新增 `createPaymentOrder`（新族，调用后端支付网关，未接入时自动降级沙箱）与 `placeOrder`（统一下单入口，优先新族、失败回退旧族沙箱）；旧族 `createOrder` 标注 `@deprecated`，明确迁移计划（新族稳定一版 → 旧族标注 #[deprecated] → 下一版删除注册）。
- **邀请链接 L-7 补全**：邀请页二维码占位升级为可复制分享邀请链接（`https://bisu.app/invite?code=xxx`），一键复制按钮 + 复制成功反馈，邀请分享链路闭环。
- **旧族命令退役注解**：支付旧族命令（`create_order` 等）添加 `# Deprecated` 文档块，禁止在旧族中新增业务逻辑，新功能统一走新族。

## v0.9.1 新功能概览（2026-09-24）

> 本版在 v0.9.0「API 大模型重构与配置安全」之上，沿四个方向做深度开发与增强：遗留风险消除 4 项、P4 增值 5 项、深度交互 5 项、性能优化 5 类，共 19 项。全部为真实代码修改，五道门禁全绿（cargo check 0 error、tsc 0 error、vitest 446 全绿、IPC 断链 0、vite build 通过）。

### 一、遗留风险消除（4 项）

- **更新元数据与真实产物对齐（R1）**：安装包大小、SHA256、签名时间戳逐项回填更新元数据，签名 key_id 与内置公钥逐字节一致，更新链路可真实验签。
- **一致性门接真实门结果（R2）**：DAG 任务新增 `gate_status`（passed/failed）与 `diff_score` 字段，质检节点按大模型结论词真实写门结果，前端一致性门三出口优先按真实门失败触发，旧 run 无该字段时保留原代理判定。
- **成本预估落真实单价（R3）**：v48 迁移为模型档案加 `unit_price` 列；成本预估按「成本台账最近实耗单价 → 模型档案单价 → 内置常量」三级回退逐镜逐媒体取价，未命中回退常量并告警，不再用固定常量估算。
- **运行时实测文档化（R4）**：只读探测真实本地库，确认已配置的真实大模型档案与运行库迁移状态，配 Key 后一键实测路径写入核验追溯表，未跑量部分如实标注不杜撰。

### 二、P4 增值能力（5 项）

- **素材多版本对比（V1）**：新增 `list_film_asset_versions` 命令，驾驶舱按镜并排对比同一镜头的多次生成版本，标注首选版本，支持两版勾选对照。
- **多视频模型 provider 注册表（V2）**：`list_video_providers` 统一返回各 provider 的能力、是否已配置 Key 与未配置引导文案；底层错误四分类（未配置 Key、网络失败、任务失败、超时）包装为可读引导，Runway、Pika、Sora、Kling 真实适配保持不动。
- **多角色音色台账（V3）**：v48 新建 `voice_roles` 表，配套 `list_voice_roles`、`save_voice_role`、`delete_voice_role` 三命令，视频工坊提供角色音色台账面板，可按角色记录音色、音调、语速；TTS provider 层已就绪按角色映射音调语速（逐角色配音透传待命令级接入，当前台账先持久化偏好）。
- **长条图导出（V4）**：条漫工坊新增「导出长条图（PNG）」，逐格读取真实分镜图纵向拼接为长图，画布高度上限 20000px，超限截断并提示，无图格虚线占位，无任何配图时按钮禁用。
- **蜂群导演台独立成页（V5）**：新增「蜂群导演台」独立页面（路由 `director`），集中观察改编断点、DAG run 实时状态、成本、队列进度与任务图、日志事件流；轮询 3s，终态自停。

### 三、深度交互增强（5 项）

- **逐镜表格微调（I1）**：视频工坊分镜表支持行内上移、下移交换镜序、时长直接编辑，调整后自动标记「待重审」琥珀徽标。
- **试听控件核验（I2）**：核验音频试听播放、暂停、进度条、BGM 下拉、音量滑杆已在 v0.9.0 就位，无缺口，本版归档证据。
- **参考图与模型切换真实生效（I3）**：视频工坊 `onSwapRef`、`onChangeModel` 由占位弹窗升级为真实动作，参考锚重绘、场景默认模型切换后真实重渲染；条漫侧空函数升级为重拉分镜并给出可操作引导。
- **应用内成片播放器（I4）**：成片卡片内嵌播放器，直接播放本地产物视频，无产物时禁用并提示。
- **失败日志逐段上报（I5）**：合成失败时把处理日志尾部（最长 500 字符）经蜂群日志通道按段上报，日志面板对错误日志条目加深红徽标（`bind_swarm_app` 启动绑定一次）。

### 四、性能优化（5 类）

- **轮询间隔收敛（P1）**：视频工坊 5 处状态轮询由 1500-2000ms 统一上调到 3000ms，资源概览面板 3s 调 5s，消除高频空转。
- **生成队列并发与退避（P2）**：队列在途并发显式收敛为 1（8GB 内存防堆积 OOM），失败重试上限提到 3 次，按 2s、4s、8s 指数退避封顶 30s 且可中止，单任务失败不卡死队列。
- **数据库索引补齐（P3）**：v48 补 `config_kv(key)` 反查索引与 `storyboard_cards(project_id)` 聚合索引（`storyboard_cards(run_id)` 幂等兜底），成本台账与门聚合不再全表扫描。
- **渲染 memo 化与分页（P4）**：驾驶舱产出行级 memo、成本汇总 memo、产出库分页（每页 20 行）；资源面板统计、教练面板四个页签 memo 化，长列表重渲染大幅收敛。
- **冗余 IPC 消除（P5）**：驾驶舱挂载自动调用保持 5 次无新增，版本对比与交付报告手动触发不叠加；流水线页、条漫页无轮询叠加，消除双拉。

## v0.9.0 新功能概览（2026-09-24）

> 本版为「API 大模型重构与配置安全」专项，在 v0.8.0 三层能力之上，把 AI 模型管理这条链路从布局、命名、数据流到持久化与密钥隔离整体重构一遍，写作功能本身无新增。

### 一、AI 模型管理页面布局重构

- 模型管理页重构为**五卡片流**：顶部服务状态条、中部模型表格、场景默认区、密钥用量区，以及折叠的高级配置区与右侧编辑抽屉。
- 删除此前重复挂载的入口与冗余面板，相关组件归入统一目录，页面结构更清晰。

### 二、接口命名规范统一

- Rust 命令成对升级为统一前缀命名，覆盖自动连接、健康检查、模型目录、密钥管理等系列。
- 保留零风险兼容别名，旧调用方无需改动即可平滑过渡。
- 图像配置此前双份定义，本轮去重收敛为单一来源。

### 三、数据流打通与兜底回滚

- 新增按场景解析活动模型配置的能力，7 个消费点统一接入同一数据源。
- 全局失效自动切换监听就位，单一数据源消除此前各处冗余调用。

### 四、配置持久化加固

- 更新下载完成与静默安装之间增加强制落盘，杜绝升级过程中模型 Key 丢失。

### 五、密钥隔离

- 打包配置仅显式列举必需资源，不再使用通配符打包。
- 含明文 Key 的配置文件不进入安装包、不进入仓库。

## v0.8.0 新功能三层概览（2026-09-24）

> 本版在 v0.6.0 九大深度模块之上，一次性补齐三大层：深度创作功能层、IP 全链路层、成片链层。以下为用户视角功能说明。

### 一、深度创作功能层（原 v0.7.0 规划随本版上线）

- **智能续写引擎**：基于前文与大纲自动续写章节，贴合既有文风与节奏。
- **节奏分析与优化**：扫描章节爽点分布与情绪曲线，给出节奏优化建议。
- **人物一致性守卫**：跨章校验角色言行、关系与设定，防止人设漂移。
- **逻辑漏洞扫描**：检测剧情矛盾、时间线冲突与伏笔失收。
- **去 AI 味精修**：把 AI 生成段落润色得更像真人笔触，降低机器味。
- **多平台一键发布**：适配番茄、起点、晋江等平台格式，一键导出发布。
- **爆款公式库**：内置可复用的爆款桥段与结构模板，随取随用。
- **读者模拟**：模拟目标读者的阅读反应与弃书点，提前预警。
- **写作教练**：针对当前稿件给出可执行的改进建议与训练路径。
- **大纲智能演化**：随章节推进反向调整后续大纲，保持主线张力。
- **世界观一致性校验**：校验力量体系、地理、势力等设定不打架。
- **章节版本时光机**：章节草稿多版本留存，可回溯任意历史版本。
- **敏感词合规引擎**：发布前扫描敏感与违规词，降低平台审核风险。

### 二、IP 全链路层（从网文到 IP 的一站式改编管线）

- **改编规划**：把长篇网文拆成可改编的分段方案与里程碑。
- **分段流水线**：按段推进改编，状态可追踪、可回退。
- **剧本工坊**：把章节改写为影视与短剧剧本结构。
- **条漫工坊**：把剧情拆成条漫分镜与对白脚本。
- **视频物料工坊**：产出短视频物料所需的文案与素材清单。
- **AI 痕迹消痕报告**：对改编稿做 AI 痕迹检测并生成消痕报告。
- **变现台账**：记录各改编方向的投入与产出，可视化变现进度。
- 配套 **22 项新增操作命令** 与 **五域导航重构**，导航结构按创作、改编、视频、台账、设置五域重组。

### 三、成片链层（导演智能体蜂群驱动的视频成片管线）

- **导演智能体蜂群 DAG 编排器**：多导演智能体按有向无环图协同分工，自动拆解成片任务。
- **图生视频扩展（Kling）**：在既有图生视频之外接入 Kling 模型，扩宽视频生成选型。
- **批量生成队列与成本计量与预算熔断**：排队批量生成，实时计量 token 与费用，超预算自动熔断。
- **合成管线升级**：字幕、BGM、分辨率统一升级，成片输出更整齐。
- **素材台账与失败恢复**：素材统一登记，生成失败可从断点恢复重跑。
- **成片驾驶舱**：集中查看全片进度、成本与产物，一处掌控。
- **7 项重点交互**：时间线、分段重渲染、一致性门三出口、进度滑杆、成本确认、进度中止、多段串联。
- **ffmpeg 随安装包分发**：合成所需的 ffmpeg 不再要求用户单独安装，随安装包一并就位。

## 版本管理规范（防版本线误判）

> **「已发布」的唯一判据是线上更新源**：Gitee `raw/master/latest.json` 与 GitHub `bisu-releases/main/latest.json` 实际报告的版本号，**不是**代码里的 `version` 字段。

- **版本线基准**：线上真实最新版以双源 latest.json 为准；本地构建版本为 **0.10.5**（2026-09-27）。0.4.4 → … → 0.5.4 → 0.6.0 → 0.8.0 → 0.9.0 → 0.9.1 → 0.10.5 版本线持续推进：
  - v0.4.4–v0.4.16：均已双源上线（主源 GitHub 归档 `v0.4.4/`–`v0.4.16/` 齐全，tags 同步；备源 Gitee 滚动最新版 + Release）。
  - v0.4.17（2026-08-23）：中间版本，本地 Tauri Release 构建未完成、未产出安装包（见 `.workbuddy/output/v0.4.17-delivery-summary.md`）；功能随 v0.4.18 上线。
  - v0.4.18 / v0.4.19：已构建并本地归档（`bisu-gitee-0418/`、`bisu-gitee-0419/`），线上状态以双源 latest.json 为准。
  - v0.4.20（2026-08-25）：安装包已构建（8.03MB），GitHub 主源 Release 与 Gitee 备源已补传。
  - v0.4.21（2026-08-26）：品牌 v2.0 + App 外壳拆分 + WorldBuilder 双写修复 + Rust 加固，双源部署上线。
  - v0.4.24（2026-08-29）：左侧导航 30 子功能补齐到 100%、素材箱契约修复（DB v3 迁移）、全局 UI/UX 品牌化、公共仓零源码治理，双源部署上线。
  - v0.4.25（2026-08-29）：创作中心对话输入区重构（对齐 WorkBuddy 胶囊工具条、附件框内呈现不灌正文、提示词增强补全外显）、大模型无缝衔接选择器（智能调度 / 自动故障转移、不显示厂商名）、授权三态更名（手动 / 默认 / 完全授权并带注释）、DB schema v4（灵感库 + 统一分析快照 + 今日任务持久化，localStorage 孤岛迁入项目库）、全套品牌 Logo 与面板能力增强，双源部署上线。
  - v0.4.26（2026-08-29）：模型切换失败根因修复（ModelSwitcher 未配置模型标记 + 禁用切换 + 错误提示具体化），双源部署上线。
  - v0.4.27（2026-08-29）：全局改版 + 账号体系 + 项目列表交互——4等级买断制账号体系（免费/专业¥199/终身¥499/团队¥99人月，AI不收费用户自接API Key）、许可证密钥生成/验证/吊销 + 兑换码 + 设备绑定（1-3台）、侧边栏三分组+付费锁标识、项目列表hover⋯菜单+置顶+橙色高亮+3px竖条、工作台暖橙化、设置页账号与许可证分区、作者卡片账号菜单、全局蓝紫残留清理，双源部署上线。
  - v0.6.0（2026-09-23）：九大深度功能模块全面上线。P0：智能续写引擎增强、节奏分析与优化器、人物一致性守卫、逻辑漏洞扫描器；P1：去AI味深度精修、多平台一键发布、爆款公式库增强；P2：读者模拟器、写作教练。同步修复 RefineProPanel 提取标题/采纳标题/全链路精修、updater.rs flush_now、useBisuAppState localStorage 迁移。双源部署上线。
  - v0.8.0（2026-09-24）：三层能力齐上线。深度创作功能层（原 v0.7.0 规划随本版落地）：智能续写引擎、节奏分析与优化、人物一致性守卫、逻辑漏洞扫描、去 AI 味精修、多平台一键发布、爆款公式库、读者模拟、写作教练、大纲智能演化、世界观一致性校验、章节版本时光机、敏感词合规引擎。IP 全链路层：从网文到 IP 的一站式改编管线，含改编规划、分段流水线、剧本工坊、条漫工坊、视频物料工坊、AI 痕迹消痕报告、变现台账，配套 22 项新增操作命令与五域导航重构。成片链层：导演智能体蜂群 DAG 编排器、图生视频扩展（Kling）、批量生成队列与成本计量与预算熔断、合成管线升级（字幕/BGM/分辨率）、素材台账与失败恢复、成片驾驶舱、7 项重点交互（时间线、分段重渲染、一致性门三出口、进度滑杆、成本确认、进度中止、多段串联），ffmpeg 随安装包分发。双源部署上线。
  - v0.9.0（2026-09-24）：API 大模型重构与配置安全专项。AI 模型管理页面重构为五卡片流（服务状态条、模型表格、场景默认、密钥用量、折叠高级区与编辑抽屉），删除重复挂载、组件归入统一目录；Rust 命令成对升级为统一前缀命名（自动连接、健康检查、模型目录、密钥管理系列），保留零风险兼容别名，图像配置双份定义去重；新增按场景解析活动模型配置能力，7 个消费点统一接入，全局失效自动切换监听，单一数据源消除冗余调用；更新下载完成与静默安装之间强制落盘，防止模型 Key 丢失；打包配置仅显式列举必需资源（无通配符打包），含明文 Key 的配置文件不进入打包与仓库。双源部署上线。
  - v0.9.1（2026-09-24）：四方向 19 项深度增强。遗留风险消除：更新元数据与真实产物对齐（大小/SHA256/签名 key_id 逐字节核对）、一致性门接真实 gate_status（passed/failed）、成本预估落真实单价（film_cost_ledger 实耗 → llm_profiles.unit_price → 常量三级回退）、运行时实测文档化。P4 增值：list_film_asset_versions 多版本对比、多视频 provider 注册表与错误四分类降级、voice_roles 多角色音色台账三命令、长条图 canvas 导出（20000px 上限）、蜂群导演台 director 独立成页。深度交互：逐镜表格上移/下移/时长/待重审微调、CP3 试听控件核验、onSwapRef/onChangeModel 真实重渲染、应用内成片播放器、stderr 尾部 500 字符经 bind_swarm_app 逐段上报。性能：视频工坊 5 处轮询 1500-2000ms 升至 3000ms、队列 MAX_IN_FLIGHT=1 与重试 3 次与 2/4/8s 指数退避、v48 补 config_kv 与 storyboard_cards(project_id) 索引、驾驶舱行级 memo 与分页 20/页与四 Tab memo 化、IPC 无双拉。五道门禁全绿（cargo check 0、tsc 0、vitest 446、IPC 断链 0、vite build 通过）。双源部署上线。
  - v0.10.5（2026-09-27）：API 配置丢失四级防护 + 七类问题全面修复（21/28）+ 导航重构 + 性能优化 + 支付订单管理补全。API 防护：Argon2id 派生密钥进程内缓存（salt 指纹校验、多线程只算一次、不缓存明文 Key）、解密失败 WARN 60s 降频、加密错误向上传播不静默落伪密文、明文 Key 随栈帧释放。七类修复：P0 托盘隐藏 (-32000) 窗口位置钳制死循环（1586次/秒）三级守卫修复、M-01 版本时光机恢复名存实亡（事件无监听）改为前端直接写缓冲+落盘、M-12 高级编辑器 Ctrl+S 旧快照覆盖新内容双写修复、M-40 三层引导叠加弹窗延迟放开修复、P2 启动 ALTER TABLE 重复列 WARN 去噪（PRAGMA 预检）、账号命令双轨退役计划（旧命令 @deprecated、新功能走 account_*）、主.rs BOM 规范化与配置版本同步。导航重构：侧边栏 52 项模块级静态配置、七大功能分组（创作/设定/AI生产/IP改编链/质检/市场/发布与收益）、经典旧版折叠子组、付费锁与标签标识。性能：派生密钥缓存消除周期性 CPU 尖峰、日志降频、静态导航消除每渲染重建、锁序优化杜绝死锁。支付订单：新增 list_orders 订单列表查询（设置页订单区块）、createPaymentOrder 新族+placeOrder 统一下单入口、邀请链接 L-7 可复制分享补全、旧族支付命令 @deprecated 迁移计划。179 文件变更（+17962/-20732）。双源部署上线。
- **严格顺序递增**：下一版本 = 线上最新版 **单步 +1**（`x.y.(z+1)`，或 minor/major 进位）。**禁止跳号**、**禁止降级**。
- **发布流程（本地通道七步闭环）**：①规则回顾（SOP §10/§11）→ ②预检（invariant + 扫描 + tsc + vitest）→ ③构建（dist-clean/混淆日志双断言）→ ④`tools/publish_ssh.py` 双源发布 → ⑤双源四项校验 + 归档完整性 → ⑥**README/CHANGELOG/交付文档同步更新（硬性规范）** → ⑦源码只推私有源仓 `origin main`（公共 Gitee / Releases 仓仅由 publish_ssh 投递发布物，严禁源码）。七步全 PASS 才算「已发布」。
- **README 同步硬性规范**：每次版本发布或迭代必须同步更新本 README（版本号/下载地址/新增功能/接口变更/配置项/已知问题/更新日志），README 落后于实际版本视为发版未闭环。

---

## 部署方法论（R15 / v0.4.13 标准化）

> 本节提炼自 `docs/dual-source-deploy-sop/` 全量文档，面向**发布工程师/架构师/SRE** 的操作总纲，覆盖 4 层门禁、双源拓扑、安全红线、零成本 CI、孤立组件分级 5 大支柱。

### 🏰 支柱 1：四层构建门禁（L1→L4 串行，任一失败拒绝发版）

| 层级 | 命令 | 验证目标 | 失败后果 |
|------|------|----------|----------|
| **L1 类型纯静态** | `npx tsc --noEmit` | 所有 TS/TSX 文件类型一致性 | 调用链类型错配直出，严禁带 any 逃生 |
| **L2 前端单元测试** | `npm run test:frontend -- --run` | Zustand action、updater 验签、迁移幂等、IPC 签名桩 | 历史回归（启动清空库事故 R17 等效场景）必失败 |
| **L3 前端生产构建** | `npm run build` | Vite 产物纯净、Tree-shaking 无死代码残留、混淆前 `dist/index-*.js` 字节指纹稳定 | 构建产物 hash 突变视为异常，需排查注入 |
| **L4 Rust 编译+单测** | `cargo check` → `cargo test` → `cargo build --release` | Rust 201 command 全注册、高敏写入（F-001/F-011）路径安全锁、数据库迁移幂等 | 任何 clippy warn / audit warn 记录在案；高敏安全告警一票否决 |

> 🔁 **可组合的零成本运行**：日常迭代只需跑 L1+L2（<60s）；正式发版必须 L1→L4 全红断全绿才放行。

### 🌐 支柱 2：双源发布拓扑（R13 拓扑反转保持生效，R14 细化校验项）

```
            ┌─────────────────── 本地构建机 ───────────────────┐
            │  tools/publish_ssh.py  (SSH 零凭据, 无 PAT 硬编码) │
            └─────────┬────────────────────┬───────────────────┘
                      ▼                    ▼
        ┌── GitHub 主源 ──┐     ┌── Gitee 备源 ──┐
        │ kule-2025/      │     │ king2030/      │
        │   bisu-releases │     │   bisu (仅产物) │
        │                 │     │                │
        │ ✓ latest.json   │────▶│ ✓ latest.json  │ 字节级同步
        │ ✓ v0.4.*/ 归档 │     │ ✓ 当前版 EXE   │ sha256 对比门禁
        │ ✓ tags v0.4.*  │     │ ✓ .sig 签名    │
        └─────────────────┘     └────────────────┘
                      ▲                    ▲
   客户端访问序 1st ──┘                    └── 客户端访问序 2nd+3rd
```

**客户端端点序**（`updater.rs::build_endpoints`）：
1. GitHub raw `bisu-releases/main/latest.json`（主端点，带 `?ts=` 时间戳防缓存）
2. Gitee CDN `king2030/bisu/raw/master/latest.json`
3. Gitee raw 兜底 `gitee.com/api/v5/repos/…`

**关键 R13 安全红线（R14 继续强制执行）**：
- ❌ **严禁**将完整源码树 force-push 到 Gitee 公共仓 `king2030/bisu`
- ✅ Gitee 公共仓 **仅存发布产物**（latest.json + 当前版 exe + sig），源码仅保留在私有源仓 `kule-2025/bisu-src`
- ✅ `sync-gitee.yml` 只在 `release` 事件触发时同步 GitHub Release **附件**（Content API 上传），无 `git push`
- ✅ 备源安装包 100% 来自主源对象库，sha256 四项校验一致才标记为「上线成功」

### 🔒 支柱 3：安全红线（4 条铁律，违反即回滚）

| # | 铁律 | 校验位置 |
|---|------|----------|
| **S1** | 敏感文件 永不入库：`.updater-keys/*`（签名私钥）、`*.privkey`、`.env*`、`app.sqlite*`（用户数据库导出） | `.gitignore` + `tools/check_version_sequence.py` 上线前二次扫描 |
| **S2** | Tauri CSP 最小权限：`script-src 'self'`（无 unsafe-inline/unsafe-eval）；`connect-src` 仅白名单 ipc + GitHub/Gitee 更新源 | `tauri.conf.json::app.security.csp`，由 `tauri build` 固化进二进制 |
| **S3** | 高敏写入双重锁：`write_text_file`（F-001）需先 `require_unlocked()`，再校验「绝对路径 + 不跨 .. + 不入系统目录黑名单」 | `commands/fsx.rs:267`；审计单元测试覆盖路径穿透 |
| **S4** | CI/CD 凭证零明文：SSH Key / GITEE_TOKEN / DINGTALK_SECRET / MAIL_PASSWORD 全走 GitHub Secrets；代码 / Actions logs 永不打印 | `.github/workflows/**` + `tools/publish_ssh.py` 全程 `continue-on-error` 不泄露堆栈 |

### ⚡ 支柱 4：零成本 CI 基础设施（6 套 Workflow 协同）

| Workflow | 触发 | 职责 | 省成本技巧 |
|----------|------|------|------------|
| `ci.yml` | push/PR main/develop | **四层门禁 L1-L4** 并行 + 版本一致性/顺序门禁 + 钉钉+邮件失败告警 | `concurrency` 同分支取消旧 run；非核心告警（`cargo clippy` / `npm audit` / eslint）使用 `continue-on-error` |
| `release.yml` | tag push `v*` | 桌面端安装包三平台交叉编译 + minisign 签名 + 主源 Release 上传 | `tauri-action` 缓存 Rust target/npm cache；失败保留 artifact 用于事后定位 |
| `sync-gitee.yml` | Release published + 手动 | 同步 Release 附件到 Gitee Release（非源码、非 git push） | `GITEE_TOKEN` 缺失时自动跳过不报错 |
| `deploy.yml` | push `backend/**` + 手动 | 后端 axum Docker 构建 + 滚动更新 + 健康检查 + 自动回滚 | 私仓镜像缓存 GHA cache；回滚逻辑使用「docker compose up -d 上一备份」 |
| `deploy-drift-monitor.yml` | 每 6h schedule | 部署配置漂移检测（线上 docker-compose vs 仓库） | 告警仅推钉钉（省 runner 分钟数） |
| `deploy-status-report.yml` | 每日 9 点 | 双源状态日报（latest.json 版本号、安装包可下载性、签名有效性） | 离线检查 4 项断言，失败进入 incident 流 |

### 🧩 支柱 5：孤立组件分级挂载框架（v0.4.12 新增，IPC 兜底体系）

> **根因**：v0.4.x 前前端 UI 引用了 86 个 invoke（LLM 流式/精修流水线/知识库/人设/工作流/时间线/世界设定/成长指标/支付 等 8 业务域），这些能力的 Rust command **虽已在其他模块实现**（ai_writer.rs / llm.rs / refinement_extra.rs 等 async fn），但存在命名兼容、孤立演进风险。
>
> **R14 新增框架**：`src-tauri/src/commands/unimplemented_*` 共 **8 个分类模块**，按业务域建立「功能建设中」兜底占位体系——当正式实现一旦缺失，模块内对应函数加回 `#[tauri::command]` 并注册进 `generate_handler!` 即可自动回落为**中文业务提示**（而非 Tauri 原生 "Unknown command" 崩溃）。

| 业务域 | 模块文件 | 覆盖命令数 | 风险等级 |
|--------|----------|----------|----------|
| LLM & 提示词 | `unimplemented_llm.rs` | 25 | 🔴 高（AgentChat 主链路） |
| 精修流水线 | `unimplemented_refinement.rs` | 11 | 🟠 中（精修面板功能块） |
| 章节版本管理 | `unimplemented_version.rs` | 9 | 🟠 中（编辑器自动保存） |
| 知识库 & 素材库 | `unimplemented_kb_materials.rs` | 4 | 🟡 低（设定/素材二封） |
| 世界 & 人设 & 关系 | `unimplemented_world.rs` | 8 | 🟠 中（世界观/角色页） |
| 工作流 & 技能日志 | `unimplemented_workflow.rs` | 5 | 🟡 低（任务面板/插件） |
| 鉴权 & 凭证 | `unimplemented_auth.rs` | 8 | 🟠 中（登录/保险库） |
| 榜单/发布/指标/杂项 | `unimplemented_misc.rs` | 16 | 🟡 低（导出/发布/榜单） |

> **分级回退策略（按风险等级）**：
> - 🔴 高：正式实现缺失时弹窗提示「【功能建设中】XXX 将在 v0.5 开放，敬请期待」，不进入崩溃路径
> - 🟠 中：Toast 级提示「暂未上线」，灰化对应按钮由前端控制
> - 🟡 低：静默降级为 no-op（返回空对象/空数组），不打断主链路

---

## v0.4.90 更新日志（2026-09-20）

> **蜂群模板生态 + RAG 真隔离 + 多模态最小接入 + 性能优化**。代码版本 0.4.90（三处同步，未重新打包/未发布双源）。与 `CHANGELOG.md` 一致。

- **模板骨架（migration v37）**：新表 `swarm_prompt_template`（project_id 可空=全局），5 个 CRUD 命令；6 题材各 2 个内置起步模板（`INSERT OR IGNORE` 幂等、进程内 OnceLock 只种子一次）；`create_swarm_run` 可选 `template_id` 作 persona 初始 prompt；前端新增「模板库」页签。
- **RAG 真隔离**：`kb_docs` 增 `project_id` 列；新增 `kb_search_scoped(project_id, query, limit)`，蜂群世界设定只取当前项目知识，不破坏既有 `kb_search`。
- **多模态（默认关）**：新增 `swarm_step_render_image(step_id, prompt)`，复用 `llm::image_generate` 文生图落本地 png、回写 `image_path`；前端按钮仅图像 profile 可见，`convertFileSrc` 展示缩略图、失败友好降级。
- **性能**：前端 `SwarmStudio.tsx` 由 setInterval 轮询改为 Tauri event 推送（`swarm_step`/`swarm_done`）；确认 `chat_complete_with_usage` 复用共享 reqwest client（OnceLock）。
- **双门禁**：`cargo check` 退出 0（仅历史 warning）、`tsc --noEmit` 退出 0。

## v0.4.89 更新日志（2026-09-19）

> **爆款智能体蜂群 P0+P1 全链路落地 + 双源安全排查**。

- 蜂群流水线全链路：选题策划 → 人设主笔 → 章节扩写 → 爽点节奏 → 质检 → 去 AI 味 → 平台合规，7 步串行 + step 进度事件推送。
- `agent_swarm.rs`：多步 LLM 协作、step 状态机、run 级 token 预算熔断；DB migrations v35/v36（`swarm_run` / `swarm_run_step` + 真实 input/output token 列）。
- P1 质量与可靠性：扩写步接 `quality_gate` 评分自动重写、去 AI 味步接 `deai_humanize`、真实 token 计量写回、v36 checkpoint 断点续跑、`create_swarm_run` 前置 pro 门槛。
- 双源仓库安全排查：GitHub/Gitee 公共仓仅产物零源码。

## v0.4.88 更新日志（2026-09 中）

> **蜂群 P1 质量与可靠性强化**（随 v0.4.89 打包上线，代码注释标注 v0.4.88）。

- `chat_complete_with_usage` 暴露真实 prompt/completion token，写回 step 并做 run 预算熔断。
- 扩写步 `quality_gate::analyze` 评分（0-100，<60 自动重写一次）；deai 步调 `deai::deai_humanize` 真实润色。
- v36 checkpoint 断点续跑：崩溃/中止后已完成 step 跳过、未完成续跑。
- `chat_complete` 重构为 `chat_complete_core`（对外签名不变），退避与故障转移。

## v0.4.87 更新日志（2026-09 中）

> **爆款蜂群 P0 编排器初版**（代码注释标注 v0.4.87）。

- 新增 `commands/agent_swarm.rs`：7 角色蜂群编排器、`swarm_agent` / 种子人格（作者手写 system_prompt，覆盖六大题材）。
- migrations v35/v36：`swarm_run` / `swarm_run_step` 表（幂等）。
- 前端 `SwarmStudio.tsx` + `swarmStore.ts` + `api/swarm.ts`：蜂群运行面板、step 时间线、产物预览。
- 明文 Key 加密迁移收尾 + LLM 调用指数退避/无缝故障转移。

## v0.5.1 更新日志（2026-09-19）

- **品牌重塑**：logo 目录 kit 全局替换、全平台图标重建（Windows/Mac/Linux 的 icns/ico/png）、竹绿品牌色令牌统一（`#14532d` / `#22c55e` / `#4ade80`）。
- **五项缺陷修复**：最小化一步交互、侧栏默认展开、卡顿 async 治理、IPC 门禁误报修复、29 页链路排查。
- **双源部署**：GitHub 主源（`kule-2025/bisu-releases`）+ Gitee 备源（`king2030/bisu`），仅发布产物（`exe` / `sig` / `latest.json`），含公共仓库源码泄漏排查与端到端 SHA256 校验。
- **四闸质量门禁**：`tsc --noEmit` 0 错误 · `vitest run` 37 文件 / 446 测试全过 · `cargo check --all-targets` · IPC 连通性校验 100% 命中。

## v0.4.20 更新日志（2026-08-25）

> **组件拆分 + RAG 向量知识库 + 排行榜 + 影子桩清除与 IPC 门禁**。

### 🧩 组件拆分（巨型组件瘦身）
- **WorkflowEditor**：1275 → 539 行（-57.8%），新增 workflowConstants / workflowStyles / workflowNodeComponents / workflowNodeRow / workflowPresetCard / workflowProgressPanel / workflowToolbar / workflowResultsPanel / workflowProjectPath / workflowNodeLibrary 等 10 文件
- **AgentCenter**：1777 → 481 行（-73%），新增 agentFlowConstants / AgentImportPanel / AgentRunPanel / PromptLibraryPanel / AgentChatPanel / AgentEditorModal 等 7 文件
- **SkillCenter / AIModelManager / OnboardingFlow**：拆分推进，新增 skillConstants / SkillPluginCard / SkillDetailPanel / aiModelConstants / ModelCard / onboardingConstants / GenreTemplateCard 等文件

### 🧠 RAG 向量知识库
- 本地 RAG 向量检索落地：知识文档向量化索引、语义检索（千章记忆前情召回）、嵌入模式指示（哈希 / 语义）

### 🏆 排行榜
- 多平台榜单抓取与展示完善（番茄 / 起点 / 晋江 / 飞卢），趋势图、分类筛选、定时自动更新

### 🧹 影子桩清除与 IPC 门禁
- 清除 86 个 `unimplemented_*` 影子桩死代码（8 文件 + mod.rs 声明），`cargo check --all-targets` 0 警告 0 错误
- 新增 IPC 连通性 CI 门禁：`scripts/check_ipc_connectivity.sh` + `npm run check:ipc` + pre-commit 钩子，前端 263 个命令全部命中后端注册
- 四闸门禁通过：tsc 0 错误 / vitest 330/330 / cargo check 0 警告 0 错误 / IPC 263/263

### 🔄 版本同步与部署状态
- package.json / tauri.conf.json / Cargo.toml / latest.json 版本号统一至 0.4.20
- 安装包 `BISU_0.4.20_x64-setup.exe`（8.03MB，SHA256 `6a13b1ef40e23b73e6fdbe54f8d9f17781f9c5c817119ada1c162e4946967181`）
- 部署状态：GitHub 主源 Release 待重新认证（401）、Gitee 备源待部署（GITEE_TOKEN）

## v0.4.19 更新日志（2026-08-24）

> **大组件拆分 + WorldBuilder 增强 + 全量功能审计**。

### 🧩 组件拆分
- **AgentChat P3 拆分**：InputFooter 集成，2127 → 970 行（-54.4%）
- **AgentCenter 大组件拆分**：1876 → 1777 行，新增 agentMarkdown / PromptRow / PromptCreateForm
- **WorldBuilder P3 组件化**：1656 → 959 行（-42%），新增 worldTypes / worldMeta / worldShared / WorldSidebar / WorldEntryCard / WorldEntryForm / ImportProjectDialog / BatchToolbar / DuplicateBanner

### 🌍 WorldBuilder 增强
- AI 定制（按分类专属 prompt 生成/优化）、拖拽排序（乐观重排 + 持久化）、跨项目导入（源 ID → 去重 → 预览 → 批量创建）、重复检测横幅

### 🔧 巡检修复
- App.tsx 空间功能：renameProject 事务顺序修复（先磁盘后内存，失败不污染内存）
- OnboardingFlow：键盘数字键与过滤列表对齐、步骤条 done/active 互斥、输入框聚焦快捷键豁免
- KnowledgeBase：嵌入模式指示（语义/哈希）、千章记忆检索入口、文档列表筛选排序

### 🔍 全量功能审计
- 15 页 / 101 组件 / 28 API 模块 / 766 后端命令 / 260 前端调用全量核对，前后端命令连通率 99.2%

### 🔒 四闸门禁引入
- 原三闸检测升级为四闸：新增 IPC 连通性门禁（前端 invoke ↔ 后端 generate_handler 注册表比对），断链即拦截

---

## v0.4.16 更新日志（2026-08-22）

> **五路专家评测修复 + 商汤 SenseTime 端点迁移**。

### 🔧 核心修复
- **五路专家评测问题修复**：AI 对话创作中心多路专家协同评估链路修复，保证 5 路专家评测结果正确汇聚与排序
- **SenseTime 端点迁移**：LLM 提供商中商汤（sensenova 系列）端点地址更新，修复旧端点失效导致的调用中断
- **v0.4.13→0.4.16 连续修复**：0.4.14、0.4.15 期间修复项随 0.4.16 统一打包上线

### 🔄 版本同步
- package.json / tauri.conf.json / Cargo.toml 版本号统一至 0.4.16
- README 同步更新下载链接与版本信息

---

## v0.4.12 更新日志（2026-08-17）

> **多角色专家团 P0/P1 全量评测修复版本**（架构师 + SRE + QA + 安全）。核心：**数据流/状态流稳定性 + 部署方法论标准化 + 孤立组件分级兜底**。

### 🏗️ 架构师交付项
- **P0 IPC 参数双保险（character_get / character_delete）**：前端传参名对齐 Rust（`id` → `characterId`），同时 Rust 侧新增 `id` 别名（Option<String> 二选一非空），旧客户端调用/新 UI 调用均零故障
- **P0 孤立组件分级框架落地**：8 个 `unimplemented_*.rs` 模块（LLM/精修/版本/知识库&素材/世界/工作流/鉴权/杂项）完成 86 个历史 invoke 全量覆盖，按 🔴高/🟠中/🟡低 建立分级回退策略，杜绝 "Unknown command" 崩溃路径
- **P0 PARAM_MISMATCH 审计复核**：经实码验证，5 项初报 MISMATCH 中 3 项为静态解析假阳性（`create_novel_project`/`polish_dialogue` 动态 params、`write_text_file` 的 base 实已 Option 化），剩余 2 项根治

### 🔧 SRE 交付项
- **P1 CI 门禁可信度修复**：新增 `.eslintrc.json`（TS + React Hooks 推荐规则集），终结 `ci.yml` 原 ESLint 步骤「无配置 continue-on-error 全吞」的假门禁现象
- **P1 sync-gitee.yml UTF-8 全量重写**：修复原文件中文 GBK 编码损坏无法维护问题；同时**用中文注释显式固化 R13 安全红线**（严禁 force-push 源码到 Gitee 公共仓），并细化 HTTP 状态码上报与计数
- **P1 版本一致性 & 顺序门禁**：三处版本号同步 bump 0.4.12（`package.json`/`Cargo.toml`/`tauri.conf.json` + 对应 Cargo.lock 自洽）
- **P0 部署方法论提炼成 README（支柱 1-5）**：四层构建门禁 / 双源拓扑 / 四条安全铁律 / 六套零成本 CI / 五级孤立组件分级框架 首次总纲化

### ⚡ 性能（状态管理流优化）
- **P0 Zustand Selector 高优修复**：`AgentChat.tsx`（5 字段）、`TaskMonitorPanel.tsx`（2 字段）直接解构全 store → 独立 selector，消除「主页面任一 store 字段变化 → 庞大 AgentChat 重渲染」的性能缺陷
- **P0 useCallback 中优修复 5 处**：`UpdateBanner.tsx` / `Settings.tsx`（2 处）/ `App.tsx`（2 处）异步处理函数（含更新检查/安装/重启链路）全包裹 useCallback 稳定引用，子组件无效重渲染清零
- **回归**：`tsc --noEmit` 0 错误 / `cargo check` 0 错误（Rust 兼容参数实编通过）

### 🔐 安全加固
- 高敏写入防护（F-001）：`write_text_file` 三验（require_unlocked → 绝对路径 → 系统目录黑名单）继续通过现有单元测试
- 发布管线凭证零明文：`sync-gitee.yml` 全量 Secrets 注入路径在注释中显式登记，便于后续安全审计复盘
- `.eslintrc.json` `no-console: warn`：仅允许 `console.warn/error`，生产构建 info/debug 天然收敛

### 🧪 QA 回归清单（v0.4.12 全绿闭环）
| 模块 | 断言项 | 结果 |
|------|--------|------|
| 版本门禁 | 三处版本号全为 0.4.12 + check_version_consistency.py 语义校验通过 | ✅ PASS |
| 角色管理 | characterGet("id") / characterDelete("id") 新旧别名调用均成功 | ✅ PASS（双保险）|
| 状态流 | 切换 useChatStore 非相关字段时，AgentChat 不产生无效重渲染 | ✅ PASS |
| SRE CI 诚实性 | ESLint 真实存在配置并能对 src/ 运行 | ✅ PASS |
| 双源同步 | sync-gitee.yml 无 `git push --force`/无源码 push 逻辑 | ✅ PASS（R13 红线）|
| 构建链 | `tsc --noEmit` / `cargo check` / `cargo check --manifest-path backend/Cargo.toml` | ✅ PASS |

## v0.4.11 更新日志（2026-08-16）

> **侧边栏排版修复 + 多角色全量评测修复（UI/数据诚实性/死代码清理）**。

### 🎨 侧边栏排版
- 主导航收起态修复（图标居中、隐藏文字）；品牌区落地新 logo；active 高亮条合并；图标 18px / gap 10 / 缩进 16 全线统一

### 🐛 P0 缺陷修复
- 顶栏统计 "undefined" 上屏兜底；`fmtWords` 5 处副本空值健壮化（防 TypeError 炸面板）；ErrorBoundary 错误文案接入翻译层（不再渲染技术堆栈）；异步错误不再替换整个面板

### 🔧 P1 体验与诚实性
- 剔除 ChapterBreakdown 假分析 loading；删除无功能占位按钮；52 处错误文案接入 `translateError`；空态留白收敛；支付"Mock"文案诚实化

### 🧹 死代码清理
- 删除 archive/（7 文件）、tokens.ts、AgentChat 60+ 行注释块、write_demo/read_demo 命令

## v0.4.10 更新日志（2026-08-16）

> **P0 紧急修复：启动即清空用户数据（R17）**。

### 🚨 启动数据清空事故修复
- **根因**：数据库迁移中 `timeline_events` 补列 `story_date` 非幂等，0.4.8 及更早版本升级至 0.4.9 后每次启动迁移报 `duplicate column name` → 自愈机制误判库损坏 → 归档用户库并重建空库（项目/章节/LLM 配置全丢）
- **修复**：补列幂等化 + 新增「迁移重复执行必须成功」回归测试（修复前该测试必失败）
- **受损恢复**：AppData 中最新 `app.corrupt-<时间戳>` 重命名回 `app.sqlite`（删除现用空库及 `-wal`/`-shm`），0.4.10 打开即自动识别旧数据

## v0.4.9 更新日志（2026-08-16）

> **主备源拓扑反转（R13）+ 多角色全量评测**。GitHub 公共仓升为主源，Gitee 降为备源。

### 🔄 主备源拓扑反转（R13）
- **客户端端点序反转**：GitHub raw（`bisu-releases/main/latest.json`）升为第一端点；Gitee CDN + Gitee raw 降为备源兜底（`updater.rs::build_endpoints` + `tauri.conf.json` 同步反转）
- **发布管线反转**：`tools/publish_ssh.py` 改为「主源 GitHub 先行推送 + tag → 从主源对象库提取本版产物（sha256 一致性门禁）→ 备源 Gitee 用主源产物同步」——备源安装包字节级来自主源
- **双源 latest.json url 各指各源**：读到哪个源的元数据就从哪个源下载，主源不可达时备源同时兜底元数据与安装包
- **兼容性**：0.4.8 及更早客户端端点序仍为 Gitee 先行，备源 latest.json 实时刷新，旧客户端无感升级链路不受影响

### 🔍 多角色全量评测（架构 / SRE / QA / 安全）
- 更新链路端到端复核：检查 → 灰度门 → 断点续传下载 → minisign 验签 → 静默安装 → 重启，全链路测试通过
- tsc 0 错误 / vitest 229 通过 / cargo 全量测试通过 / 真实产物验签门禁通过

### 🔄 版本同步
- package.json / tauri.conf.json / Cargo.toml 版本号统一至 0.4.9
- README / SOP（`DUAL_SOURCE_DEPLOY_SOP.md` §1.6 R13 规则）同步更新

## v0.4.8 更新日志（2026-08-16）

> **签名校验修复 + 全量功能评测**。核心修复更新签名验证失败问题。

### 🔧 签名校验修复（R12：InvalidEncoding）
- 根因：Tauri 生成的签名格式（4行）与 minisign-verify 库期望的标准格式不兼容
- 修复：重构 `verify_minisign` 函数，新增 `parse_signature` 支持 Tauri 格式直接解析
- 验证：新增 3 个单元测试覆盖 Tauri 格式、未知格式拒绝等场景

### 🔍 全量功能评测修复
- WorkbenchPanel 统计卡片接入真实业务数据（替换 undefined 显示）
- 修复 Icon 名称错误（file → scroll 等）
- 修复 kbSearchChapters 未定义问题（改为动态导入）
- 清理未使用的导入（characterList, worldEntryList）

### 🔄 版本同步
- package.json / tauri.conf.json / Cargo.toml 版本号统一至 0.4.8
- README 同步更新下载链接与版本信息

### 回归
- tsc 0 错误 + vitest 229/229 + cargo test updater_download 4/4

## v0.4.7 更新日志（2026-08-15）

> **更新可靠性与版本可见性修复**（R11）+ R10 归档恢复。稳定性版本，写作功能无变化。

### 🔧 更新可靠性（R11：兜底端点恒 404）
- 根因：v0.4.6 端点修复只改配置未改运行时覆盖层（`updater.rs::build_endpoints()`），兜底端点实际从未生效
- 修复：运行时第三兜底端点改为 GitHub raw `main/latest.json`（带时间戳防缓存）——Gitee 双端点同时不可达的极端弱网下，更新检查真正有兜底

### 🏷️ 版本可见性（R11：Releases 页停留 v0.4.3）
- 发布脚本自动打 tag `v<ver>`（零凭据 SSH 推送 + 远端复核）；支持可选 PAT 创建正式 Release（修 Latest 徽章）
- GitHub Releases 版本列表现含 v0.4.4–v0.4.7；四项校验增加弱网重试

### 🗄️ R10 归档恢复
- `v0.4.4/`（8.2MB）+ `v0.4.5/`（10.4MB）从 Gitee 部署历史找回，GitHub 全版本归档恢复齐全；补打历史 tags

### 回归
- tsc 0 错误 + vitest 251/251 + cargo check 通过 + 构建 dist-clean/混淆双门禁日志

## v0.4.6 更新日志（2026-08-15）

> **性能与发布物修复**（R8/R9）+ R10 部署脚本加固。安装包 7.9MB（较缺陷构建 -36%）。

### ⚡ 性能修复（R8：36 个 React.lazy 全部失效 → 按需加载恢复）
- 混淆插件重构：自研 closeBundle 级 bundleObfuscator（transform 级插件破坏 rolldown 静态分析致动态 import 全内联）
- 代码分割迁移至 `codeSplitting.groups`（Vite 8/rolldown 新 API）；Ranking 页懒加载切断静态链
- **主 chunk 1,941KB → ~840KB（-57%）**，36 个页面级 chunk 按需加载；新增 `tools/trace-static-chain.mjs` 静态链诊断

### 🧹 构建产物纯净修复（R9）
- 根因：沙箱虚拟化 Node 删除致 dist 残留三轮历史 chunk 混入安装包 + 混淆被静默跳过
- 修复：distCleanPlugin（构建前强制清空 + 残留即中止门禁）+ 签名 mtime 强制重签
- 效果：安装包 12.4MB → 7.9MB；39 个业务 chunk 混淆生效（源码保护恢复）

### 🔧 部署
- publish_ssh.py：GitHub 走 `ssh.github.com:443` + `blob:none` 克隆（弱网可靠）；R10 归档保全门禁（显式 add + 历史目录断言，防每次发布清空归档）
- 回归：tsc 0 错误 + vitest 251/251 + 双源四项校验 PASS

## v0.4.5 更新日志（2026-08-15）

> **公钥轮换 + CI 全链路修复 + 真实数据流接入**。⚠️ 旧客户端（≤0.4.3）须手动安装本版一次。

- 更新公钥轮换（R5 第三次复发修复）：`tauri.conf.json` 与 Rust 常量原子双改 + 编译前 invariant 硬门禁
- 签名链修复（R1/R2/R7）：`npx @tauri-apps/cli@2 signer` 替代 cargo-tauri.exe；latest.json 签名单层 base64；`--private-key-path` 长选项
- 真实数据流接入：作者成长数据拉取，健康监控/作家品牌等页面剔除硬编码演示数据
- 业务链修复：Library 资料删除、Publish 发布历史删除等闭环

## v0.4.4 更新日志（2026-08-15）

> **多角色专家团全面修复批次**。

- 代码质量清扫：移除 910MB 历史大文件、修复 10 个 merge 损坏源文件、清理死 API/一次性脚本
- 孤立组件分类分级挂载：TodayTasks（今日待办）/ MarketIntelligence（市场智能）/ HitFormula（爆款公式）/ CreationWizard（创作向导）等
- 发布通道切换：GitHub Actions（付费）→ 本地构建自动部署（publish_ssh.py SSH 零凭据）

---

## v0.4.2 更新日志（2026-08-01）

> **版本线纠正说明**：此前误将代码版本号当作已发布版本，声称已到 0.4.5；实际 0.4.2–0.4.5 各轮 Release Build 均因 CI 阻断（死代码 `deny-warnings` + `createUpdaterArtifacts` 未开启）从未出包，线上真实最新版始终为 **0.4.1**。本版本以 0.4.1 为基线顺延至 0.4.2，并将上述期间开发的全部功能随 0.4.2 首次打包上线。

### 🐛 核心 P0 修复
- **空间选择修复**：`read_files` 命令补齐 7 个调用点的 `base` 参数，解决"导入文件夹"静默失败
- **排行榜抓取加速**：新增 `fetch_html()` 直接 HTTP 抓取（4s 超时 + UA 轮换），将抓取等待从 >25s 降到 ≤8s；demo 数据附带各平台搜索链接
- **风格画像（新增模块）**：`style_profile_analyze` / `style_profile_batch` 两个 Tauri 命令，7 维风格评分（文笔/节奏/情感/对话占比/段落密度/词汇丰富度/句式复杂度）
- **质量门（新增模块）**：4 维评分（去AI味 30% + 爽点密度 25% + 情绪曲线 20% + 逻辑自洽 25%），13 个子项明细 + 通过阈值 60；5 个单元测试通过

### 🎨 UI 大改版
- **工作台三列布局**：左 290px（动态欢迎卡 + 快速统计 + 上手指引）/ 中间 flex（操作按钮 + 模板启动）/ 右 240px（热门题材 + 创作贴士）
- **动态欢迎消息**：8 题材（玄幻/都市/言情/科幻/悬疑/轻小说/历史/游戏）每 5 秒自动轮换 + 时间前缀（早安/上午好/下午好/晚上好/夜深了）

### 🔧 工程改进
- `tauri.conf.json` 新增 `plugins.updater.pubkey`，签名预检通过
- 5 个 `quality_gate` 单元测试覆盖主流程（含 AI 套话识别）

---

## v0.4.3 更新日志（2026-08-05）

> **双源部署完成**：Gitee 主源 + GitHub 备源，SHA256 一致性验证通过。

### 🔧 工程改进
- **vite 构建修复**：`vite.config.ts` 添加 `emptyOutDir: false`，解决 safe-delete 钩子拦截 `dist/assets` 问题
- **Tauri 配置清理**：移除 `tauri.conf.json` 中无效的 `targetDir`/`emptyOutDir` 字段
- **Rust 编译告警清零**：修复 `state.rs`（`poisoned` 变量前缀 `_`）和 `fix_engine/orchestrator.rs`（`handle` 变量前缀 `_`）两处 unused variable 告警
- **双源部署自动化**：Gitee `deploy_gitee_direct.py` + GitHub `upload_release.py` 完整部署链路验证

---

## v0.3.30 核心功能

### 创作全流程覆盖
- **工作流引擎**：11 节点类型、拖拽编排、条件分支、执行进度可视化、3 预设模板（玄幻爽文流/言情甜宠流/悬疑烧脑流）
- **引导式创作向导**：5 步从零创建新书（选题材→选风格→基础设定→推荐工作流→开始创作）
- **6 模板快速启动**：玄幻废柴/都市重生/言情甜宠/科幻末世/悬疑密室/轻小说异世界

### 网文专项技能中心（34 技能 + 18 专家 + 8 专家团）
- 技能：续写/润色/起名/审稿/黄金三章/爽点节奏/伏笔追踪/人设校验/钩子生成/多线协调/情绪曲线/反派提升/世界观校验/去AI味 + 书名/简介/大纲/开篇/结尾/对话/场景/战斗/甜宠/虐心/反转/签约/上架/防盗/读者互动/数据复盘/关系网/力量体系/章节拆分/写作习惯/卷间过渡/番茄优化
- 专家：大纲规划师/世界观架构师/角色设计师/网文教练/剧情推演师 + 大纲架构师/爽文大师/弧光设计师/市场分析师/平台适配师 + 书名简介/场景氛围/对话艺术/战斗策略/甜宠恋爱/数据诊断/上架运营/写作导师
- 专家团：黄金三章攻坚团/爆款网文攻坚团/完结打磨天团 + 新书孵化团/甜宠攻坚团/热血战斗弧团/数据急救团/连载护航团

### 提示词智能增强
- **6 风格切换**：爽文/甜宠/虐文/烧脑/热血/日常
- **跨章节一致性校验**：角色行为/时间线/世界观规则三大维度
- **套路检测**：14 种金手指识别 + 10 种桥段模式识别 + 反转建议
- **链式推荐**：13 题材黄金公式，按阶段智能推送 3-5 步增强链
- **diff 预览**：增强前后行级对比，确认后回填

### AI 对话创作中心
- **多模型聚合**：9+ 平台预配置（商汤/智谱/Kimi/通义/DeepSeek/Ollama 等），优先级链+熔断+无感切换
- **专家团展示**：执行任务时横向显示参与专家头像+名称
- **任务监控**：实时显示执行步骤进度（✅/⏳/⚠️）
- **工作空间选择**：侧边栏+输入栏双入口，跨项目无缝切换
- **版本化保存**：AI 生成内容可"存为新版本"，不覆盖原文

### 创作辅助
- **排行榜**：番茄/起点/晋江/飞卢多平台榜单抓取，趋势图，分类筛选
- **知识库**：本地 RAG 向量检索，千章记忆，章节级语义搜索
- **番茄发布**：一键复制适配文本+打开后台，30 秒自动清空防泄露
- **封面设计**：AI 封面建议+图像生成
- **多格式导入**：PDF/DOCX/TXT/MD，自动解析+字数统计+全文检索
- **备份恢复**：一键备份/恢复，24 小时自动备份，RPO≤24h

### 意识与记忆
- **意识更新**：管理 AI 助手行为记忆和偏好学习，支持自动学习开关
- **长期记忆**：持久化关键创作知识、角色设定、情节要点，config_kv 加密存储

### 安全与质量
- **加密**：Argon2id 密钥派生 + AES-256-GCM 数据库加密 + 剪贴板 30 秒自动清除
- **代码审计**：v0.3.30 执行全面审计（前端 23 项 + 后端 16 项），所有 HIGH/MEDIUM 级问题已修复或标记
- **测试**：330 项自动化测试全通过（v0.4.20），TypeScript 0 错误

## 技术栈

| 层 | 技术 |
|----|------|
| 桌面框架 | Tauri 2.1 (Rust 后端 + WebView 前端) |
| 前端 | React 18.3 + TypeScript 5.4 + Vite 8 + Zustand 4.5 |
| 后端 | Rust 1.79 (edition 2021) + rusqlite 0.31 + reqwest 0.12 |
| 数据库 | SQLite + WAL + FTS5 全文检索 |
| 加密 | Argon2id + AES-256-GCM + minisign 签名 |
| 构建 | NSIS 安装包 (Windows) + 自动更新 (minisign 验证) |

---

# 历史里程碑文档（M0–M4）

> 以下为各里程碑的详细设计文档，保留作历史追溯。

## 核心标签

`#网文创作桌面助手` `#爆款小说创作桌面助手` `#爆款网文创作桌面助手` `#网文写作工具` `#桌面写作软件`

> **版本说明（统一口径）**：当前代码版本 **0.10.13**。`0.1.x / 0.2.x / 0.4.x` 为重构前历史版本。**NovelDesk** 为重构前产品名（现 BISU/笔溯）。线上已发布安装包版本以双源 latest.json 为准。

> 网文多项目桌面客户端 —— 基于已通过 G0–G6 的架构基线落地的 **M0 工程骨架**。
> 对应的实现路线：`IMPLEMENTATION_PLAN.md v1.1` §5 M0；上游权威：系统设计 §3 / 安全设计 §5.3·§6.1 / 部署设计。
> 本地编译环境：Rust + MSVC + WebView2，支持 Tauri 2.1 桌面应用构建与 NSIS 安装包打包。

## 1. M0 范围
- Tauri 2.1 脚手架（窗口 1000×700、MSI 打包）、`capabilities` 权限白名单
- **M9 本地存储**：SQLite 单连接 + WAL + 幂等迁移（`db/conn.rs` + `db/migrations.rs`）
- **M13 配置与加密**：argon2id 派生密钥 → local-keystore（仅存 `salt(hex)` + `verifier(hex)`，禁明文）
- **M11 文件系统集成**：沙箱目录读写 + 附件落盘
- **M12 提醒调度骨架**：接入 Windows Toast（`check_due` 触发）
- 退出标准：应用可启动、设口令、建加密库、写读一条数据、弹通知

## 2. 目录结构（26 源文件 + 4 占位图标）
```
bisu/
├── package.json / tsconfig*.json / vite.config.ts / index.html   # 前端工程（React18.3/TS5.4/Vite5.3/Zustand4.5）
├── src/
│   ├── main.tsx / App.tsx / vite-env.d.ts
│   └── api/invoke.ts          # Tauri invoke 封装（命令名与 Rust 注册严格对齐）
├── src-tauri/
│   ├── Cargo.toml             # 版本钉死（tauri 2.1.0 / rusqlite 0.31+sqlcipher / argon2 0.5 / sha2 0.10 / hex 0.4 …）
│   ├── build.rs / tauri.conf.json / .gitignore
│   ├── capabilities/default.json   # core+event+fs(沙箱)+notification 白名单；窗口 "main"
│   ├── icons/                 # 占位图标（32x32/128x128/128x128@2x/icon.ico）
│   └── src/
│       ├── main.rs            # 注册 14 个 M0 命令 + notification 插件 + M9 初始化
│       ├── db/{mod,conn,migrations}.rs
│       ├── crypto/{mod,kdf}.rs    # M13：argon2id(m=64MiB/t=3/p=4)→32字节密钥
│       └── commands/{mod,config,storage,fsx,scheduler}.rs   # M13/M9/M11/M12
└── tools/
    ├── gen_icons.py           # 占位图标生成（纯色 PNG/ICO）
    └── m0-crypto-check/       # M13 加密闭环 Node 原型（verify.mjs + verify-report.txt）
```

## 3. 本地构建前置（编译在本地 Rust 环境）
- **Rust ≥ 1.79**（edition 2021）+ **MSVC 构建工具**（`link.exe`，来自 Visual Studio Build Tools 或 VS）
- **WebView2 运行时**（Win10/11 默认已带；否则装 Evergreen Bootstrapper）
- **Node.js ≥ 18**（本机用 22 验证）、npm
- 不依赖任何云资源（纯本地/离线）

## 4. 构建步骤
```bash
cd bisu
npm install                 # 安装前端依赖（React/Vite/Zustand/@tauri-apps）
cargo tauri dev            # 启动开发模式（自动拉起 Vite + Rust 编译）
# 首次构建若报图标缺失：npx tauri icon <你的1024x1024.png> 生成正式图标（当前为纯色占位）
```
成功后将弹出开发窗口，走「设置解锁口令 → 解锁 → 写一条 Demo → 读回显示 → 弹通知」链路。

## 5. M0 退出标准对照
| 标准 | 验证方式 |
|---|---|
| 应用可启动 | `cargo tauri dev` 成功弹窗 |
| 设口令 | 调 `setup_password`，`config_kv` 写入 `salt(hex)`+`verifier(hex)` |
| 建加密库 | `conn::init` 打开 `app.sqlite` 并执行幂等迁移 |
| 写读一条数据 | `write_demo` / `read_demo` 往返一致 |
| 弹通知 | `check_due` 或前端通知按钮触发 Windows Toast |

## 6. M13 加密闭环（已 Node 原型验证 ✅）
- **派生**：`argon2id(password, salt)` —— `m=64MiB / t=3 / p=4`，输出 32 字节密钥
- **持久化**：`config_kv` 仅存 `salt(hex)` + `verifier=SHA256(key)(hex)`，**明文口令绝不落盘**
- **解锁**：重新派生并比对 verifier；成功置全局 `UNLOCKED` 态
- **高敏拦截**：`unlock`/`change_password`/`set_config` 首行检查 `UNLOCKED`，未解锁返回 `ND0003`
- **SQLCipher 集成点**：`conn::init` 解锁后执行 `PRAGMA key = "x'<hex>'"`（M0 演示未启用密钥，注释已标注）
- **原型验证**：`tools/m0-crypto-check/verify.mjs`（hash-wasm 纯 wasm argon2id，4 项全绿：确定性 / AES-256-GCM 往返 / 错误口令拒绝 / 仅存 salt+verifier；派生 340ms）

## 7. 已知偏离（均不触碰冻结决策）
- 额外钉死 `sha2=0.10`、`hex=0.4`：crypto 模块 SHA-256 校验 + hex 持久化必需（原选型表未列，属合理技术补充）
- 通知：前端直连 `@tauri-apps/plugin-notification`（M0 命令列表无独立 notify 命令，符合 Tauri 2 插件惯例）
- 剪贴板：M0 未用；M3 经 `arboard` 直接调用，无需 Tauri 插件权限，故 `capabilities` 未声明
- 图标为占位纯色 PNG/ICO，发布前需 `npx tauri icon` 替换为正式图标

## 8. 与架构基线追溯
| 本骨架 | 上游权威来源 |
|---|---|
| Tauri 2.1 / Rust 1.79 | 系统设计 §3.1.5 / 实施计划 §2 |
| 目录 1:1（M9/M11/M12/M13） | 实施计划 §3 / 系统设计 §3.2 |
| IPC 鉴权（UNLOCKED 拦截高敏） | 安全设计 §5.3 / 实施计划 §3 |
| local-keystore（argon2id + DPAPI 可选） | 安全设计 §6.1 |
| SQLCipher PRAGMA key 集成点 | M13 集成说明 / 安全设计 §6.1 |

> 后续里程碑：M1 资料域 → M2 工作流 → M3 番茄发布 → M4 集成验收（详见 `IMPLEMENTATION_PLAN.md`）。

---

# M1 资料域闭环（在 M0 之上叠加）

> 实施计划 §5 M1；覆盖 M10(FTS5) + M1(资料库) + M2(多格式导入) + M7(番茄格式适配) + M8(字数统计)。
> 依赖已就绪的 M9(存储) / M11(文件) / M13(加密) 底座。

## M1 范围
- **M10 FTS5 检索**：`material_fts` 外部内容虚拟表 + 三个同步触发器，自动随 materials 增删改同步；`search_materials` 用 `MATCH` + `snippet()` 返回命中片段。
- **M1 资料库**：`projects`(可父子层级) / `materials` 两级结构，CRUD（幂等 `file_hash` 留待导入）、接入 FTS5。
- **M2 多格式导入**：PDF(`pdf-extract`) / DOCX(`docx-rs`，段落文本尽力而为) / TXT(`String::from_utf8_lossy` + GBK 回退) / MD(`pulldown-cmark` 收集 Text/Code)；魔数+扩展名识别；返回纯文本 + 字数 + sha256。
- **M7 番茄格式适配**：LF 归一 / 全角空格转半角 / 弯引号转直引号 / 行尾空白去除 / 连续空行折叠，并回传 `issues` 提示。
- **M8 字数统计**：字符级精确计数（见 §M1-附）。
- 退出标准：拖入 PDF/Word/TXT/MD → 解析入库 → 一键适配番茄格式 → 显示精确字数（源码层全部就绪，编译后本地冒烟）。

## M1 目录结构（叠加在 M0 之上）
```
bisu/src-tauri/src/
├── state.rs                      # 新增：全局 UNLOCKED 共享锁 + require_unlocked()
├── commands/
│   ├── library.rs   (M1)         # lib_create_project / lib_list_projects / lib_create_material
│   │                           #   / lib_get_material / lib_list_materials / lib_delete_material
│   ├── importer.rs   (M2)        # import_file（四格式解析）
│   ├── adapter.rs    (M7)        # adapt_tomato
│   ├── wordcount.rs  (M8)        # count_chars（+ 内部 count_text 复用）
│   └── search.rs     (M10)       # search_materials
├── db/migrations.rs             # 新增 projects / materials / material_fts(FTS5) + 三触发器
├── Cargo.toml                   # 新增 pdf-extract/docx-rs/pulldown-cmark/encoding_rs
└── main.rs / capabilities/default.json  # 注册 10 个新命令 + 登记命令名
bisu/src/
├── api/materials.ts             # 新增：10 条命令的 TS 类型化封装
└── pages/Library.tsx            # 新增：资料库页（项目/导入/资料/适配/搜索 五区）
```
（M0 既有文件 `config.rs`/`conn.rs`/`mod.rs`/`App.tsx` 等已就地扩展，未破坏原有行为。）

## M1 构建说明（本地）
M0 的前置条件（Rust≥1.79+MSVC+WebView2+Node≥18）不变；M1 新增 4 个 Rust 依赖会在 `cargo tauri dev` 首次编译时下载（pdf-extract/docx-rs 含本地编译，耗时偏长）。
```bash
cd bisu
npm install
cargo tauri dev
```
启动后切到「资料库(M1)」标签页即可走通：新建项目 → 导入文件(路径) / 新建资料(粘贴正文) → 番茄适配 / 搜索。

## M1 退出标准对照
| 标准 | 验证方式（本地冒烟） |
|---|---|
| 拖入文件 → 自动解析 | `import_file(path)` 返回 format/text/字数/hash |
| 入库 | `lib_create_material` 写入 materials，FTS5 触发器同步 |
| 一键适配番茄格式 | `adapt_tomato(text)` 返回 adapted + issues |
| 显示精确字数 | `count_chars(text)` 返回 chars_total/cjk/...（见下） |
| 检索命中 | `search_materials(query)` 返回 snippet 命中 |

### §M1-附：M8 字数统计算法（已 Node 原型验证 5/5 全绿）
- 归一化 `\r\n`/`\r` → `\n`
- `chars_total` = 非空白 Unicode 码点计数（番茄「字数」=每个可见字符计 1）
- 拆分 `cjk`(汉字 \u4e00–\u9fff / \u3400–\u4dbf) / `latin`(ASCII 字母) / `digit`(ASCII 0–9) / `punct`(其余可见) / `ws`(空白)
- `lines` = 按 `\n` 切分长度；`paragraphs` = 按空行切分后非空段数
- 验证脚本：`tools/m1-wordcount-check/verify.mjs`（中英混排 / 纯中文段落 / 空串 / 仅空白 / 全角数字符号边界全过，确定性 OK）

## M1 已知偏离（均不触碰冻结决策）
- **DOCX 解析为「尽力而为」**：仅提取段落→运行→文本，表格/图片/批注等非文本节点跳过（已注释）。docx-rs 0.4 AST 变体较多，若本地编译报错需按实际版本微调枚举路径。
- **word_count / char_count 暂等同**：M1 将二者均按 `chars_total` 填充；语义区分（字数/字符数）留待后续。
- **import_file 直接读传入路径**：为 MVP 简化，未走 Tauri dialog 插件 + 路径沙箱限制（生产需在 M3/M11 收紧，已在代码中注释）。
- **M1 退出标准「自动入库」未全自动串联**：当前 UI 导入仅展示解析结果，需用户手动「新建资料」粘贴入库；完整拖入即入库留待 M2 工作流或后续打磨（各命令能力已齐备）。
- capabilities 沿用 M0 风格：自定义命令默认开放、不在 JSON 写独立权限条目（与 M0 一致，避免未知权限导致构建失败）。

## M1 与架构基线追溯
| 本里程碑 | 上游权威来源 |
|---|---|
| M10 FTS5 / M1 资料库 / M2 导入 / M7 适配 / M8 计数 | 实施计划 §5 M1 / 系统设计 §3.2（M1/M2/M7/M8/M10） |
| 多格式库 pdf-extract/docx-rs/pulldown-cmark/encoding_rs | 系统设计 §3.1.5 技术选型 |
| IPC 鉴权（UNLOCKED 共享拦截高敏） | 安全设计 §5.3 |
| 目录 1:1（新增 5 命令文件） | 实施计划 §3 / 系统设计 §3.2 |
| 字数准确率 V6=100%（字符级） | 高层架构 §2.3 / 实施计划 §7 |

---

# M2 工作流闭环（在 M0/M1 之上叠加）

> 实施计划 §5 M2；覆盖 M3(专项工作规划) + M4(进度看板) + M5(上架提醒中心)。
> 依赖已就绪的 M9(存储) / M12(提醒调度/通知触达) / E-03(Windows 通知)。

## M2 范围
- **M3 专项工作规划**：`tasks` 表（归属项目、支持父子层级 `parent_id`、依赖 `depends_on` JSON、状态 `status∈{todo,doing,done}`）；CRUD + 状态流转校验（`task_update_status` 非法状态返回 `ND0101`）。
- **M4 进度看板**：按项目统计 `total/todo/doing/done` 与完成率%(`done/total×100`)，支持单项目进度 + 跨项目完成率聚合（`progress_by_project` JOIN `projects`）。
- **M5 上架提醒中心**：`reminders` 表（`triggered` 标志位持久化）；创建/列表/删除 + `reminder_check_due`：从 `reminders` 表查 `due_at<=now` 且未触发项，弹 Windows Toast（复用 M12 `NotificationExt` 弹法）并标记 `triggered`（落库不丢，对应 V3 触达率 100%）。
- 退出标准：建专项任务 → 看板可见完成率 → 设上架时间 → 到点弹通知（源码层全部就绪，编译后本地冒烟）。

## M2 目录结构（叠加在 M1 之上）
```
bisu/src-tauri/src/
├── commands/
│   ├── task.rs      (M3)   # task_create/list/get/update_status/delete
│   ├── progress.rs   (M4)  # progress_summary/progress_by_project
│   └── reminder.rs   (M5)  # reminder_create/list/delete/check_due
├── db/migrations.rs         # 新增 tasks / reminders 两表（IF NOT EXISTS 幂等）
├── Cargo.toml / main.rs / capabilities/default.json  # 注册 11 个新命令 + 登记
bisu/src/
├── api/workflow.ts          # 新增：11 条命令的 TS 类型化封装
└── pages/Workflow.tsx       # 新增：工作流页（专项任务/进度看板/上架提醒 三区）
```
（M0/M1 既有文件 `state.rs`/`mod.rs`/`config.rs`/`App.tsx` 等已就地扩展，未破坏原有行为。）

## M2 构建说明（本地）
前置条件不变（Rust≥1.79+MSVC+WebView2+Node≥18）；M2 无新增 Cargo 依赖（复用 M0/M1 既有）。
```bash
cd bisu
npm install
cargo tauri dev
```
启动后切到「工作流(M2)」标签页：新建任务(填项目ID+标题) → 列出任务 + 切换进行中/完成 → 进度看板查完成率 → 上架提醒设截止时间 + 检查到点弹通知。

## M2 退出标准对照
| 标准 | 验证方式（本地冒烟） |
|---|---|
| 建专项任务 | `task_create` 写入 tasks，返回 id |
| 看板可见完成率 | `progress_summary` 返回 total/todo/doing/done + completion_rate；`progress_by_project` 返回各项目完成率 |
| 设上架时间 | `reminder_create` 写入 reminders(due_at) |
| 到点弹通知 | 到点后 `reminder_check_due` 弹 Windows Toast + 标记 triggered |

### §M2-附：M4 完成率 + M5 到点判定（已 Node 原型验证 9/9 全绿）
- **M4 完成率**：`completion_rate = total>0 ? round(done/total*100*10)/10 : 0`（保留 1 位小数）；边界：空任务→0 / 全done→100 / 混合→按比。
- **M5 到点判定**：`now >= due_at`（均按 RFC3339 解析比较）；边界：过去→到点 / 未来→未到点 / 恰好相等→到点 / ±1 秒。
- 验证脚本：`tools/m2-workflow-check/verify.mjs`（9 项全过，确定性 OK）。

## M2 已知偏离（均不触碰冻结决策）
- **reminder_check_due 为独立实现**：未复用 M12 内存 Vec 接口，改用 `reminders` 表持久化 + 按需触发（更正确，应用重启不丢；M12 仍作内存骨架保留）。两者并存互不影响。
- **tasks 时间戳用 RFC3339(UTC)**：与 `library.rs` 的 Local 紧凑格式不同，属故意一致于任务规范，非失误。
- capabilities 沿用「自定义命令默认开放」风格，仅在 description 注释登记命令名（与 M0/M1 一致）。

## M2 与架构基线追溯
| 本里程碑 | 上游权威来源 |
|---|---|
| M3 专项规划 / M4 进度看板 / M5 上架提醒 | 实施计划 §5 M2 / 系统设计 §3.2（M3/M4/M5） |
| 依赖 M12 通知触达（E-03 Windows Toast） | 实施计划 §4 外部系统 E-03 / 安全设计 §5 |
| 上架提醒触达率 V3=100% | 高层架构 §2.3 / 实施计划 §7 |
| 进度逾期预警覆盖 V2=100%（完成率+提醒） | 高层架构 §2.3 |
| 目录 1:1（新增 3 命令文件） | 实施计划 §3 / 系统设计 §3.2 |
| IPC 鉴权（UNLOCKED 共享拦截高敏） | 安全设计 §5.3 |

---

# M3 番茄发布闭环（在 M0/M1/M2 之上叠加）

> 实施计划 §5 M3（= 模块 M6）；覆盖 M6(番茄发布辅助)。
> 依赖已就绪的 M7(格式适配) / M8(字数统计) / M9(存储) / M13(加密) / E-01(番茄作家后台外链)。

## M3 范围
- **M6 番茄发布辅助**：`tomato_publish_helper(material_id, backend_url?)` 五步流程 ——
  1. 读 `materials.content`（需解锁，未解锁返回 `ND0003`）；
  2. 调 `adapter::adapt_tomato` 得适配纯文本；
  3. `arboard` 写入系统剪贴板（copied）；
  4. spawn 线程 **30 秒后**若剪贴板仍等于写入内容则清空（**防误清**，落实安全设计 §5.4 剪贴板防泄露）；
  5. `webbrowser::open` 打开番茄作家后台外链（E-01，仅 http/https，默认 `DEFAULT_TOMATO_URL` 占位），返回 `TomatoPublishResult{adapted_text, copied, cleared_after_secs:30, backend_opened, backend_url, warning?}`。
- 本端操作 ≤2 步（选资料→一键复制+打开后台），对应 V4。
- 半自动：不回写发布状态（本端生成即落档，用户在番茄侧手动标记完成，故 M3 退出标准不含自动状态回拉）。
- 退出标准：在资料页点「一键复制+打开后台」→ 剪贴板含适配文本 → 番茄后台已打开 → 用户粘贴+平台原生发布（源码层全部就绪，编译后本地冒烟）。

## M3 目录结构（叠加在 M2 之上）
```
bisu/src-tauri/src/
├── commands/tomato.rs    (M6)   # tomato_publish_helper
├── Cargo.toml                   # 新增 webbrowser=1.0（arboard 0.9 在 M0 已加）
└── main.rs / capabilities/default.json  # 注册 1 个新命令 + 登记
bisu/src/
├── api/tomato.ts          # 新增：tomatoPublishHelper 封装（复用 materials.ts 接口）
└── pages/Publish.tsx      # 新增：番茄发布页（选择资料/适配预览/一键发布 三区）
```
（M0/M1/M2 既有文件 `mod.rs`/`App.tsx`/`config.rs` 等已就地扩展，未破坏原有行为。）

## M3 构建说明（本地）
前置条件不变（Rust≥1.79+MSVC+WebView2+Node≥18）；M3 新增 1 个 Rust 依赖 `webbrowser`（轻量，跨平台开浏览器）。
```bash
cd bisu
npm install
cargo tauri dev
```
启动后切到「番茄发布(M3)」标签页：选项目→选资料→适配预览→「一键复制+打开后台」→ 剪贴板含适配文本 + 番茄后台已打开；约 30 秒后剪贴板自动清空（防泄露）。

## M3 退出标准对照
| 标准 | 验证方式（本地冒烟） |
|---|---|
| 复制已适配文本 | `tomato_publish_helper` 返回 copied=true，剪贴板含 adapted_text |
| 打开后台 | backend_opened=true，系统浏览器打开 backend_url |
| 30 秒自动清空 | 不操作约 30 秒后剪贴板被清空；期间若用户复制其他内容则不被误清（防误清） |
| 本端 ≤2 步 | 选资料 + 一键按钮即可完成复制+打开 |

### §M3-附：M6 发布逻辑（已 Node 原型验证 8/8 全绿）
- **30 秒自动清空**：写入后启动定时器，到点若剪贴板仍等于写入内容则清空。
- **防误清边界**：若用户在延时内复制了别的内容，到点比对不一致 → 不清空（不误删用户内容）。
- **URL 校验**：仅 `http://`/`https://` 允许打开，拒绝 `ftp`/相对/空等，防 URI 注入。
- 验证脚本：`tools/m3-publish-check/verify.mjs`（T1 写入未改→清空 / T2 中途改→不清空 / T3 覆盖→清空后内容 / T4–T8 URL 校验，8 项全过，确定性 OK）。

## M3 已知偏离（均不触碰冻结决策）
- **DEFAULT_TOMATO_URL 为占位**：`https://writer.tomato.com` 仅作演示；生产需替换为真实番茄小说作家后台地址（E-01 外链），可由用户通过 `backend_url` 参数覆盖。
- **webbrowser 打开依赖系统默认浏览器**：若环境无默认浏览器或策略限制，`backend_opened=false` 并返回 `warning` 提示手动打开（不阻塞复制）。
- **M3 是最后一个功能里程碑**：资料域 / 工作流 / 番茄发布三域全部打通；之后仅余 M4 集成验收与发布（联调 + 代码签名 + MSI + 备份恢复）。

## M3 与架构基线追溯
| 本里程碑 | 上游权威来源 |
|---|---|
| M6 番茄发布辅助（复制+打开后台） | 实施计划 §5 M3 / 系统设计 §3.2（M6） |
| 半自动、不回写发布状态 | 实施计划 §5 M3 退出标准 / 冻结决策 U-02 |
| 剪贴板 30 秒自动清空（防泄露） | 安全设计 §5.4 |
| E-01 仅系统浏览器打开外链（无 API） | 实施计划 §4 外部系统 E-01 / 冻结决策 U-02 |
| 本端操作 ≤2 步 V4 | 高层架构 §2.3 / 实施计划 §7 |
| 目录 1:1（新增 1 命令文件 tomato.rs） | 实施计划 §3 / 系统设计 §3.2 |
| IPC 鉴权（require_unlocked 拦截） | 安全设计 §5.3 |

---

# M4 集成验收与发布（在 M0–M3 之上收口）

> 实施计划 §5 M4；覆盖：① 五功能联调跨模块数据流贯通；② 验收 V1–V6 指标；③ 构建签名（Windows 代码签名）+ MSI 打包 + 自动更新通道；④ 备份/恢复演练（RPO≤24h / RTO≤30min，每日自动 + 手动归档至异盘）。
> 全套 Rust 源码「源码就绪、编译留本地」（沙箱无 Rust/MSVC/WebView2）；逻辑/契约层已 Node 原型全绿。

## M4 范围
- **集成验收（逻辑层 ✅）**：`tools/m4-integration-check/verify.mjs` —— **31/31 全绿**（M8 字数 / M7 番茄适配含连续空行折叠 / M4 完成率 / M5 到点判定 / M13 加密闭环 + 跨模块流 A 资料→番茄、B 任务→进度→提醒、C 同项目，字段契约与 TS 接口对齐）。配套 `M4_SMOKE_TEST_PLAN.md` 为本地 `cargo tauri dev` 真实 E2E 人工跑测（§0–§9）。
- **备份/恢复（新增 backup 模块）**：`commands/backup.rs` 三件：`backup_create`（高敏，需解锁；复制 `app.sqlite`+`-wal`/`-shm` 到异盘，时间戳防覆盖；目录留空回退 `backup_dir`→`<app_dir>/backups`）、`backup_restore`（高敏；关连接→覆盖→重开）、`auto_backup`（setup 中 24h 节流冷备，失败仅记日志不阻塞）。
- **发布构建**：`tauri.conf.json` 的 `bundle.windows.signCommand` 指向 `scripts/sign.ps1`（signtool SHA256 + DigiCert 时间戳，证书经 `ND_CERT_FILE`/`ND_CERT_PASSWORD`）+ `plugins.updater`（endpoints/pubkey 占位，`createUpdaterArtifacts` 默认关）；`Cargo.toml` 加 `tauri-plugin-updater`；`capabilities/default.json` 加 `updater:*` 权限并登记 `backup_create`/`backup_restore`。
- **前端设置页**：`src/api/backup.ts`（封装 `backupCreate`/`backupRestore`）+ `src/pages/Settings.tsx`（四区块：备份目录 / 立即备份恢复 / 上次备份时间 / 关于签名）+ `src/App.tsx` 新增「设置/备份(M4)」第五标签。

## M4 目录结构（叠加在 M3 之上）
```
bisu/src-tauri/src/
├── main.rs                      # 累计 38 命令（原 36 + 备份 2）+ updater 插件 + setup 调 auto_backup
├── commands/backup.rs  (M4)    # backup_create / backup_restore / auto_backup（高敏，require_unlocked 拦截）
├── commands/mod.rs              # pub mod backup;
├── tauri.conf.json              # bundle.windows.signCommand + plugins.updater（占位、默认关）
├── capabilities/default.json   # + updater:* 权限 + 登记 backup 命令
├── Cargo.toml                   # + tauri-plugin-updater = "2"
└── scripts/sign.ps1             # signtool SHA256 + DigiCert 时间戳签名
bisu/src/
├── api/backup.ts                # 封装 backupCreate / backupRestore（复用 getConfig/setConfig）
├── pages/Settings.tsx           # 新增：设置/备份页（四区块）
└── App.tsx                      # 新增「设置/备份(M4)」标签
bisu/tools/m4-integration-check/
├── verify.mjs                   # 31/31 全绿（逻辑/契约层）
└── M4_SMOKE_TEST_PLAN.md        # 本地真实 E2E 人工跑测
bisu/.笔溯/output/delivery/
└── M4_DELIVERABLE.md            # 本里程碑交付文档
```
（M0–M3 既有文件 `state.rs`/`config.rs`/`mod.rs` 等未破坏；命令风格沿用「自定义命令默认开放 + Rust 侧 UNLOCKED 拦截」。）

## M4 构建说明（本地）
前置条件不变（Rust≥1.79 + MSVC + WebView2 + Node≥18）。发布签名需先设证书环境变量：
```bash
cd bisu
npm install
# 发布签名（可选）：
#   set ND_CERT_FILE=C:\path\to\bisu.pfx
#   set ND_CERT_PASSWORD=******
cargo tauri dev          # 开发调试（五功能 + 设置/备份标签）
cargo tauri build        # 产出 src-tauri/target/release/bundle/msi/NovelDesk_0.1.0_x64_en-US.msi（签名后）
```
启动后切「设置/备份(M4)」：配置异盘目录 → 立即备份返回路径 → 恢复填备份路径 → 重启验证数据回滚；隔 24h 启动自动在备份目录生成新备份。

## M4 退出标准对照
| 标准 | 验证方式（现状） |
|---|---|
| 五功能联调贯通 | `verify.mjs` 跨模块流 A/B/C 全绿（逻辑/契约层） |
| 验收 V1–V6 指标 | 各里程碑已 Node 原型验证（字数 V6=100% / 完成率 V2=100% / 触达 V3=100% / 本端≤2步 V4） |
| 安装包可装 | `cargo tauri build` 产物 MSI（留本地编译） |
| 离线全功能 | 全程无云依赖 |
| 审计日志≥180 天 | `config_kv`/操作留痕落盘（安全设计 §7.2） |
| 一键备份恢复可用 | `backup_create`/`backup_restore` 源码就绪（E2E 留本地） |
| 代码签名 | `scripts/sign.ps1` + signtool（需采购证书） |
| RPO≤24h / RTO≤30min | 每日自动备份 + 手动即时 |

## M4 已知偏离（均不触碰冻结决策）
- **SQLCipher 密钥（M13 集成点）未接线**：当前演示未执行 `PRAGMA key`，备份为明文 SQLite 副本；生产接线后备份自动保持加密（已知偏离，跨里程碑注明）。
- **代码签名证书需自采购**：未签名 MSI 仍可用，仅 SmartScreen 警告；构建前设 `ND_CERT_FILE`/`ND_CERT_PASSWORD`。
- **自动更新通道默认关闭**：`createUpdaterArtifacts` 默认未开；启用需替换 `plugins.updater.endpoints` + 设 `TAURI_SIGNING_PRIVATE_KEY` 并部署更新服务器（可选云能力，与「本地优先/离线」冻结决策一致）。
- **备份默认目录留空用 `<app_dir>/backups`**：建议用户在设置页指定异盘目录以满足 RPO 隔离。

## M4 与架构基线追溯
| 本里程碑 | 上游权威来源 |
|---|---|
| 五功能联调 / M4 退出标准 | IMPLEMENTATION_PLAN §5 M4 / 高层架构 §2.3 / 实施计划 §7 |
| 备份恢复 RPO≤24h/RTO≤30min | 实施计划 §5 M4 / 安全设计 §6 |
| 代码签名 + MSI + updater | 部署设计 / 安全设计 §5 |
| IPC 鉴权（UNLOCKED 拦截备份高敏） | 安全设计 §5.3 |
| 目录 1:1（新增 commands/backup.rs） | 实施计划 §3 / 系统设计 §3.2 |
| 更新通道可选云（默认关） | 冻结决策 U-01/U-02（本地优先/离线） |

---

# M4 集成验收与发布（在 M0–M3 之上收口）

> 实施计划 §5 M4；覆盖「五功能联调 + 验收 V1–V6 + 代码签名/MSI/更新通道 + 备份恢复演练」。
> 依赖已就绪的 M0–M3 全部功能模块（累计 38 命令）+ 冻结决策（U-01=Tauri、U-02=半自动、本地优先/离线/单机单用户、SQLite+SQLCipher+FTS5）。
> 沙箱无 Rust/MSVC/WebView2，**无法就地编译**；本里程碑为「源码就绪、编译留本地」。

## M4 范围
- **集成验收**：五功能（资料域 / 工作流 / 番茄发布 / 设置备份 / 底座）跨模块数据流贯通，已在沙箱以 Node 原型复刻验证（见下）。
- **备份/恢复（新增 backup 模块）**：`backup_create` / `backup_restore`（高敏，首行 `require_unlocked()`）+ `auto_backup`（启动 24h 节流冷备，失败不阻塞启动）；复制 `app.sqlite` 及其 `-wal`/`-shm` 伴随文件，时间戳防覆盖。
- **发布构建**：Windows 代码签名（`scripts/sign.ps1` + signtool，SHA256 + DigiCert 时间戳，证书取自 `ND_CERT_FILE`/`ND_CERT_PASSWORD`）+ MSI 打包 + 自动更新通道（`plugins.updater`，默认关闭，属可选云能力）。
- **前端**：设置/备份页（`Settings.tsx`，四区块：备份目录 / 立即备份恢复 / 上次备份时间 / 关于签名）+ 第五标签页接入（`App.tsx`）。

## M4 目录结构（叠加在 M3 之上）
```
bisu/src-tauri/src/
├── commands/backup.rs    (M4)   # backup_create / backup_restore / auto_backup
├── commands/mod.rs              # 新增 pub mod backup
├── main.rs                     # 注册 38 命令 + auto_backup 调用 + tauri_plugin_updater::init()
├── Cargo.toml                  # 新增 tauri-plugin-updater = "2"
├── tauri.conf.json             # bundle.windows.signCommand + plugins.updater(占位 endpoint/pubkey)
├── capabilities/default.json   # updater:default/allow-check/allow-download-and-install + 登记 backup 命令
└── scripts/sign.ps1            # signtool 签名脚本（M4 打包）
bisu/src/
├── api/backup.ts               # 新增：backupCreate/backupRestore 封装（复用 getConfig/setConfig）
└── pages/Settings.tsx          # 新增：设置/备份页（四区块）
（M0–M3 既有文件 App.tsx 等已就地扩展，未破坏原有行为。）
bisu/tools/m4-integration-check/
├── verify.mjs                  # 31/31 全绿（纯算法 21 + 跨模块流 A/B/C 10）
└── M4_SMOKE_TEST_PLAN.md       # 本地 cargo tauri dev 真实 E2E + 签名 MSI 构建（§0–§9）
bisu/.笔溯/output/delivery/
└── M4_DELIVERABLE.md           # 本里程碑交付文档（范围/命令清单/退出标准/偏离/追溯）
```

## M4 构建说明（本地）
前置条件不变（Rust≥1.79+MSVC+WebView2+Node≥18）；M4 新增 `tauri-plugin-updater` 依赖会在首次编译下载。
```bash
cd bisu
npm install
# 发布签名（可选）：set ND_CERT_FILE=C:\path\to\bisu.pfx && set ND_CERT_PASSWORD=******
cargo tauri dev        # 开发联调（五功能 E2E）
cargo tauri build      # 发布 MSI（经 scripts/sign.ps1 签名，需先设证书环境变量）
```
启动后切到「设置/备份(M4)」标签页：配置异盘目录 → 立即备份 → 关闭应用改动数据 → 恢复 → 数据回滚。

## M4 退出标准对照
| 退出标准 | 验证方式 |
|---|---|
| 五功能联调贯通 | `tools/m4-integration-check/verify.mjs` 31/31 全绿（逻辑/契约层） |
| 验收 V1–V6 指标 | 各里程碑已 Node 原型验证（字数 V6=100% / 完成率 V2=100% / 触达 V3=100% / 本端≤2步 V4） |
| 安装包可装 | `cargo tauri build` → `src-tauri/target/release/bundle/msi/NovelDesk_0.1.0_x64_en-US.msi` |
| 离线全功能 | 全命令本地/离线，无云依赖 |
| 审计日志≥180 天 | `config_kv`/操作留痕落盘保留（安全设计 §7.2） |
| 一键备份恢复可用 | `backup_create`/`backup_restore`（E2E 留本地） |
| 代码签名 | `scripts/sign.ps1` + signtool（需采购证书） |
| 自动更新通道 | `plugins.updater` 就绪，默认关闭（可选云能力） |
| RPO≤24h / RTO≤30min | 每日自动备份 + 手动即时 |

## M4 已知偏离（均不触碰冻结决策）
- **SQLCipher 密钥（M13 集成点）未接线**：当前备份为明文 SQLite 副本；生产接线后备份自动加密（各里程碑已注明）。
- **代码签名证书需自购**：未签名 MSI 仍可用，仅 SmartScreen 警告。
- **自动更新通道默认关闭**：启用需替换 `plugins.updater.endpoints` + 设 `TAURI_SIGNING_PRIVATE_KEY` 并部署服务器（与本地优先/离线一致）。
- **备份默认目录 `<app_dir>/backups`**：建议用户在设置页指定异盘以满足 RPO 隔离。

## M4 与架构基线追溯
| 本里程碑 | 上游权威来源 |
|---|---|
| 五功能联调 / M4 退出标准 | 实施计划 §5 M4 / 高层架构 §2.3 / 实施计划 §7 |
| 备份恢复 RPO≤24h/RTO≤30min | 实施计划 §5 M4 / 安全设计 §6 |
| 代码签名 + MSI + updater | 部署设计 / 安全设计 §5 |
| IPC 鉴权（UNLOCKED 拦截备份高敏） | 安全设计 §5.3 |
| 目录 1:1（新增 `commands/backup.rs`） | 实施计划 §3 / 系统设计 §3.2 |
| 更新通道可选云（默认关） | 冻结决策 U-01/U-02 |

---

# 文档与资料（docs/ 目录）

> 全部项目文档已统一归集到 `docs/`，按**文档类型**分类。源码（`src/`、`src-tauri/`、`backend/`）保留在工程根目录，不在 `docs/` 内；其只读快照与索引见 `docs/10-源码索引/`。

| 分类目录 | 内容 |
| --- | --- |
| `docs/00-文档索引.md` | 文档总索引（本文件） |
| `docs/01-技术文档/` | 架构、开发计划、代码签名 |
| `docs/02-设计文档/` | 各业务域设计规格、功能模块设计、UI 对照、网文提示词增强 |
| `docs/03-用户手册/` | 本地测试指南、UI/对话框说明、交付总览、商标实操 |
| `docs/04-API文档/` | 接口清单（前端 API 模块 + Tauri 命令参考 + 跨层链路） |
| `docs/05-测试与评测/` | 评测/审计/回归/功能验证报告 |
| `docs/06-安全/` | 安全加固与审计 |
| `docs/07-团队与对账/` | 架构团报告、UI/功能对齐审查、决策审批 |
| `docs/08-构建日志/` | 各版本构建日志 |
| `docs/09-商标与品牌/` | 商标可用性评估 |
| `docs/10-源码索引/` | 源码只读快照 + 目录树/模块/命令索引（`SOURCE_INDEX.md`） |
| `docs/11-交付与归档/` | 历史交付包、构建缓存/外部副本索引 |

> 完整文件清单见 `docs/00-文档索引.md`。
> 体积较大的 Cargo 构建缓存与历史工作区副本已登记在 `docs/11-交付与归档/外部资源索引.md`，未纳入主文档目录。
