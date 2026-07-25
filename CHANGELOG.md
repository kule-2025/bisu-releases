# BISU 笔溯 — CHANGELOG

> 面向中文网文 / 小说创作的一站式桌面工作台（Tauri 2 + React 18 + Rust）

---

## [v0.3.7] — 2026-07-25

### 🔧 修复与优化

#### 类型安全加固
- **P0 全局修复**：18 个文件共 61 处 `catch (e: any)` → `catch (e: unknown)`，消除 TypeScript TS18046/TS2339 类型错误
- **7 处类型错误精确修复**：`AgentCenter.tsx`（`e.name` → `e instanceof Error`）、`Ranking.tsx` / `EnhancePromptPanel.tsx` / `OutlineManager.tsx`（`e?.message` → `e instanceof Error ? e.message : String(e)`）

#### Rust 后端数据完整性
- `export_pub.rs`：UUID v4 第三字段格式修复（5位 → 4位 + 手动版本位 `0x4000`），符合 UUID 规范
- `refinement.rs`：`generate_id()` 毫秒时间戳 → `rand::random::<u128>()`，彻底消除批量写入时 ID 主键冲突风险
- `import_export.rs`：父目录创建失败不再静默吞入，改为将错误加入 `errors` 数组供用户追溯
- `export_pub.rs`：`unix_seconds_to_date()` 改用 chrono 库替代手工闰年推算，消除跨年日期精度风险

#### TypeScript 前端健壮性
- `ChapterEditor.tsx`：AI 续写取消语义修复（`AbortController` → `isCancelled ref` 模式），取消按钮真正生效
- `App.tsx`：`as any` 类型断言 → 完整 `NovelProjectInfo` 默认值，消除类型绕过

### 🧪 测试回归
- Vitest：186 测试全部通过
- Rust cargo check：0 error / 0 warning
- TypeScript tsc：0 error

### 📦 构建产物
| 文件 | 大小 | 说明 |
|------|------|------|
| `BISU_0.3.7_x64-setup.exe` | 6.4 MB | NSIS 安装器（推荐） |
| `BISU_0.3.7_x64.exe` | 23 MB | 绿色便携版 |

---

## [v0.3.6] — 2026-07-24

> 全维度深度修复版本，覆盖安全/架构/性能/代码质量/技术债务六大维度。

### 🔒 安全
- `fsx.rs`：`write_text_file` 路径沙箱加固（`resolve_within` + 系统目录拒绝列表）
- 后端 CORS 生产化：从 `permissive()` 改为环境变量驱动白名单

### 🏗 架构
- `utils.rs`：`now_ts()` / `resolve_within()` 公共工具函数统一（消除 6+ 处重复）
- `migrations.rs`：重复建表 SQL 合并（outline_nodes 表创建×2 → ×1）
- `knowledge.rs`：LLM embedding 查询 LRU 缓存（128项 SHA-256 键）

### 🐛 Bug 修复
- `deai.rs`：`extract_chapter_title` 正文截断误命中修复
- `deai.rs`：`local_rewrite` 离线改写无脑追加文字修复
- `world_entries.rs`：`world_entry_update` `unwrap_or_default` 静默丢弃非法值修复
- `refinement.rs`：`generate_id` 毫秒时间戳主键冲突修复

### 💅 代码质量
- 清理 unused imports × 5、deprecated API × 1
- 静默 dead_code 误报 × 9 模块
- 修复 unused variables × 3
