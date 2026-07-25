# v0.3.6 发布说明

**发布日期**: 2026-07-25

## 版本概述

BISU 网文桌面创作助手 v0.3.6 — 面向网文初学者的桌面创作工具，帮助小白作者创作各类题材年度爆款并成长为大神。

## 技术栈

- **桌面框架**: Tauri v2（Rust 后端 + React/TypeScript 前端）
- **前端**: React 18 + TypeScript 5.4 + Vite 5.3
- **本地存储**: SQLite（加密）+ Key-Value 配置
- **打包工具**: Tauri NSIS 打包（Windows）

## 六大业务域

1. **账号/商业化** — 会员分级（free/regular/premium × 月/季/年）、支付网关
2. **排行榜** — 12:00 定时刷新、历史趋势图表
3. **技能中心** — 技能启用/禁用、依赖管理、执行日志
4. **作品管理** — 章节编辑器、版本快照/恢复、炼字工坊
5. **大模型** — 优先级链、熔断状态机、ACL 控制
6. **文件管理** — 文档按类型分类、源码只读快照

## 验证基线

- `cargo check`: 0 error / 0 warnings
- `cargo test`: 146 passed
- `vitest`: 186 passed
- `tsc --noEmit`: 0 error

## 系统要求

- Windows 10/11 x64
- 4GB RAM 以上
- 200MB 可用磁盘空间
