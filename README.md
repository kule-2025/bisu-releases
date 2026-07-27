# BISU 网文创作桌面助手

> 面向网文创作者的桌面端工具，辅助创作、提升效率，助力产出爆款内容。
> **源码存放于私有仓库** `kule-2025/bisu-src`，本仓库仅含安装包和更新元数据。

## 核心定位

**网文创作桌面助手** | **爆款小说创作桌面助手** | **爆款网文创作桌面助手**

BISU 是一款跨平台桌面应用，专为网文创作者打造。集成六大业务模块，覆盖从素材收集、大纲规划、正文写作到发布运营的全流程，帮助创作者高效产出年度爆款。

## 产品特点

| 特性 | 说明 |
|------|------|
| 🖥️ **桌面端原生体验** | 基于桌面应用框架构建，轻量高效，原生 UI 性能 |
| ✍️ **智能写作辅助** | 笔潮创作链路，从素材→大纲→人物→正文→润色，全流程 AI 辅助 |
| 📊 **爆款内容策略** | 排行榜实时监控，热门题材数据驱动，精准把握市场趋势 |
| 🧩 **六大业务模块** | 账号/商业化、排行榜、技能中心、作品管理、大模型、文件管理 |
| 🔄 **版本管理** | 章节版本快照与恢复，多阶段编辑，安全不丢稿 |
| 🤖 **大模型支持** | 多模型优先级链、熔断保护，本地/云端智能切换 |
| 🔔 **无感自动更新** | 后台静默检查 + 非侵入式横幅提示，一键下载并自动重启（Gitee 主源 + GitHub 备源） |

## 技术栈

- **桌面框架**: 基于跨平台桌面框架（Rust 后端 + React/TypeScript 前端）
- **前端**: 现代 Web 技术栈
- **本地存储**: SQLite（AES-GCM 加密）+ Key-Value 配置
- **打包**: NSIS（Windows）

## 下载

最新版本：**v0.3.12**（2026-07-27）

- GitHub 源（备源）：[BISU_0.3.12_x64-setup.exe (7.7 MB)](https://raw.githubusercontent.com/kule-2025/bisu-releases/main/packages/BISU_0.3.12_x64-setup.exe)
- Gitee 源（主源）：[BISU_0.3.12_x64-setup.exe](https://gitee.com/king2030/bisu/raw/main/BISU_0.3.12_x64-setup.exe)
- 应用内「检查更新」会自动从双源拉取最新版。

### 当前版本

- **v0.3.12** (2026-07-27) — Windows x64 安装包（9 项截图问题修复 + 更新签名密钥轮换）
- 下载: [BISU_0.3.12_x64-setup.exe (7.7 MB)](https://raw.githubusercontent.com/kule-2025/bisu-releases/main/packages/BISU_0.3.12_x64-setup.exe)

### SHA256 校验

```
setup.exe:  3d77e1f6bccff18ee368380495d9b7cd6d3d6e7e129465270e0b6ce01eedb2c7
```

## 版本历史

| 版本 | 日期 | 说明 |
|------|------|------|
| v0.3.12 | 2026-07-27 | 9 项截图问题修复（排行榜导航/抓取提示/技能中心/左侧导航/欢迎页/工作台/LLM 模型不存在/SenseNova/智能体对话页）+ 更新签名密钥轮换（公钥 79044C0B488CA03C → 9B6BFF206F56CB22） |
| v0.3.9 | 2026-07-26 | 源码保护部署 + 无感更新机制对齐；修复应用内更新黑屏/闪屏 |
| v0.3.8 | 2026-07-26 | 无感自动更新、双 endpoint、竞品清理、侧边栏图标修复 |

## 标签

`#网文创作` `#小说创作` `#桌面应用` `#爆款网文` `#写作助手` `#网文写作工具` `#desktop-app` `#novel-writer`

## 仓库结构

| 路径 | 用途 |
|------|------|
| `packages/` | 已编译的安装包文件（.exe）与 minisign 签名（.sig） |
| `latest.json` | 应用内自动更新元数据 |
| `docs/` | 发布说明、版本日志 |

## 安全说明

- 本仓库严禁存放源代码文件
- 源码仅存放于 `kule-2025/bisu-src`（私有仓库）
- 敏感信息存放于 GitHub Secrets / CI 变量，不提交到仓库
- 本仓库仅包含已编译安装包、签名与文档，确保源码安全

## 双源更新

- **主源（Gitee）**：`https://gitee.com/king2030/bisu/raw/main/latest.json`
- **备源（GitHub）**：`https://raw.githubusercontent.com/kule-2025/bisu-releases/main/latest.json`

> 客户端优先拉取 Gitee，失败自动回退 GitHub，保证国内用户更新稳定。

## 相关仓库

- **源码仓库**（私有）: `kule-2025/bisu-src`
- **Gitee 发布仓库**: `gitee.com/king2030/bisu`
- **GitHub 发布仓库**: `kule-2025/bisu-releases`

## 许可

私有许可，详见 GitHub 私有仓库源码许可协议。
