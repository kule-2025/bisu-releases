# BISU 版本发布仓库

> 此仓库仅包含 BISU 网文桌面创作助手的**已编译安装包**和**发布说明**。
> **源码存放在私有仓库** `kule-2025/bisu-src`，本仓库严禁存放任何源代码。

## 仓库结构

| 路径 | 用途 |
|------|------|
| `packages/` | 已编译的安装包文件（.exe） |
| `docs/` | 发布说明、版本日志 |
| `.github/` | CI/CD 自动化配置 |

## 下载

最新版本请前往 [Releases 页面](https://github.com/kule-2025/bisu-releases/releases)。

### 当前版本

- **v0.3.6** (2026-07-25) — Windows x64 安装包

## 安全说明

- 本仓库严禁存放 `.rs`、`.ts`、`.tsx` 等源代码文件
- 源码仅存放于 `kule-2025/bisu-src`（私有仓库）
- 敏感信息存放于 GitHub Secrets，不提交到仓库
