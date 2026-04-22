# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

Stash 代理客户端工具集，通过 jsdelivr CDN 静态托管。包含两类内容：

1. **Stash 代理脚本** (`stash_js/`) — 运行在 Stash 代理客户端中的 JavaScript 脚本，用于检测各服务的可用性（Claude、ChatGPT、Gemini、Netflix、YouTube Premium）
2. **Node Scout 节点侦察** (`stash_js/fraud_score_*`) — IP 欺诈评分检测工具，含代理注入脚本、Web UI 和 CLI 批量检测脚本

## 技术栈

- 纯静态文件，无构建系统、无包管理器、无测试框架
- JavaScript (ES6)：Stash 代理脚本和 Web UI
- Bash + jq：`fraud_score_check.sh` CLI 工具
- Stash `.stoverride` (YAML)：代理客户端配置

## CDN 分发

文件通过 jsdelivr CDN 分发，URL 格式：
```
https://fastly.jsdelivr.net/gh/ericwu917/stash-kit@master/path/to/file
```
`.stoverride` 文件中引用的脚本和图标均使用此 CDN 地址。修改文件路径或文件名时需同步更新 `.stoverride` 中的引用。

## 关键约定

- **Stash 脚本环境**：`stash_js/` 下的脚本运行在 Stash 代理客户端的 JavaScript 引擎中，可用 `$httpClient.get/post`、`$notification.post`、`$persistentStore`、`$done()` 等 Stash 专有 API。不能使用 Node.js 或浏览器 DOM API。
- **fraud_score_proxy_inject.js 例外**：这是 HTTP 重写脚本，拦截 `http://fraud-check.stash/` 请求并返回完整 HTML 页面，内含嵌入式 UI，因此会包含 HTML/CSS/JS 混合内容。
- **语言**：代码注释和 UI 文本混合使用中英文。与用户交流时使用中文。
- **图标**：`icons/` 目录下为 PNG 格式，在 `.stoverride` 中通过 CDN URL 引用。

## 文件关联

- `stash_override/AI-Tiles.stoverride` → 引用 `stash_js/claude.js`、`chatgpt.js`、`gemini.js`
- `stash_override/Streaming-Tiles.stoverride` → 引用 `stash_js/youtube_premium.js`、`netflix.js`
- `stash_override/Fraud-Score-Check.stoverride` → 引用 `stash_js/fraud_score_proxy_inject.js`，配置 HTTP 重写规则
- `fraud_score_ui.html` 是 Node Scout 的独立 Web UI 版本，与 `fraud_score_proxy_inject.js` 中的嵌入 UI 功能相同但独立运行
