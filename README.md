# 笔溯 BISU — 网文新手作者专属桌面创作工具

## 核心标签

`#网文创作桌面助手` `#爆款小说创作桌面助手` `#爆款网文创作桌面助手` `#网文写作工具` `#桌面写作软件`

> **当前代码版本：0.6.0**（2026-09-23；`package.json` / `Cargo.toml` / `tauri.conf.json` 三处同步；安装包随发布管线双源推送，线上版本以 latest.json 为准）

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
| **GitHub 主源（推荐）** | https://raw.githubusercontent.com/kule-2025/bisu-releases/main/v0.6.0/BISU_0.6.0_x64-setup.exe |
| **Gitee 备源（国内直连）** | https://gitee.com/king2030/bisu/releases/download/v0.6.0/BISU_0.6.0_x64-setup.exe |
| **更新元数据（主源）** | https://raw.githubusercontent.com/kule-2025/bisu-releases/main/latest.json （备源：`gitee.com/king2030/bisu/raw/master/latest.json`） |

> 下载后双击安装即可。Windows 10/11 x64，无需额外运行时依赖。
> **部署策略（2026-08-16，v2.0 / R13 拓扑反转）**：双源 = **GitHub 公共仓主源**（`bisu-releases`：根 latest.json（主端点，url 指向本仓 `v<ver>/` 归档）+ 全版本归档 + tags）+ **Gitee 公共仓备源**（`king2030/bisu` raw/master：latest.json + 当前版 exe/sig，**产物由发布管线从主源字节级同步**，sha256 一致性门禁）。客户端端点顺序：GitHub 主源 → Gitee CDN → Gitee raw 备源。发布通道为**本地构建自动部署**（`tools/publish_ssh.py` SSH 零凭据，主源先行 → 备源同步），GitHub Actions 已停用（零成本硬约束）。完整规范见 `docs/dual-source-deploy-sop/双源部署标准化流程文档.md`。

### 已知问题（v0.4.27）

- **品牌升级 v2.0**：新钢笔橙 Logo（`#EF6014`）与全套图标已随 v0.4.21 上线，启动画面 / 关于页视觉将呈现新品牌形象。
- **部署状态**：v0.4.21 双源部署完成后，本「已知问题」区将同步更新；历史 v0.4.20 的 Release 认证与 Gitee 同步问题已随本轮部署一并解决。

## 版本管理规范（防版本线误判）

> **「已发布」的唯一判据是线上更新源**：Gitee `raw/master/latest.json` 与 GitHub `bisu-releases/main/latest.json` 实际报告的版本号，**不是**代码里的 `version` 字段。

- **版本线基准**：线上真实最新版以双源 latest.json 为准；本地构建版本为 **0.6.0**（2026-09-23）。0.4.4 → … → 0.5.4 → 0.6.0 版本线持续推进：
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

> **版本说明（统一口径）**：当前代码版本 **0.4.90**。`0.1.x / 0.2.x` 为重构前历史版本。**NovelDesk** 为重构前产品名（现 BISU/笔溯）。线上已发布安装包版本以双源 latest.json 为准。

> 网文多项目桌面客户端 —— 基于已通过 G0–G6 的架构基线落地的 **M0 工程骨架**。
> 对应的实现路线：`IMPLEMENTATION_PLAN.md v1.1` §5 M0；上游权威：系统设计 §3 / 安全设计 §5.3·§6.1 / 部署设计。
> 当前环境（沙箱）无 Rust/MSVC/WebView2，**无法就地编译**；本骨架为「源码就绪、编译留本地」。

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
