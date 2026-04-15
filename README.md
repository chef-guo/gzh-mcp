# GZH MCP Server

一个基于 MCP 协议的微信公众号发布工具，让 AI 助手可以直接将 Markdown 文章排版并发布到微信公众号草稿箱。

## 功能特性

- **发布文章**：一键将 Markdown 转为微信富文本并上传到公众号草稿箱
- **预览文章**：渲染 Markdown 为公众号样式 HTML，不发布，方便调整确认
- **主题管理**：内置多套精美排版主题，支持自定义主题注册/删除
- **自动处理图片**：本地图片、网络图片、相对路径图片全部自动上传素材库
- **远程 Server 模式**：解决本地 IP 不在公众号白名单的问题
- **多公众号支持**：配合 Server 模式管理多个公众号

## 快速开始

**安装**

```bash
npm install -g gzh-mcp
```

**Claude Desktop 配置 (`claude_desktop_config.json`)：**

```json
{
  "mcpServers": {
    "gzh-mcp": {
      "command": "gzh-mcp",
      "env": {
        "WECHAT_APP_ID": "your_app_id",
        "WECHAT_APP_SECRET": "your_app_secret"
      }
    }
  }
}
```

## 工具说明

### `preview_article` — 预览文章

渲染 Markdown 为公众号样式 HTML，**不发布**，用于确认排版效果。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `content` | string | ✅ | Markdown 正文（含 frontmatter） |
| `theme_id` | string | | 主题 ID，默认 `default` |

### `publish_article` — 发布文章

排版并发布到公众号草稿箱。

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `content` | string | | Markdown 正文 |
| `content_url` | string | | 远程 Markdown 文件 URL |
| `file` | string | | 本地 Markdown 文件路径 |
| `theme_id` | string | | 主题 ID |
| `app_id` | string | | 多公众号时指定 AppID（需 Server 模式）。**普通模式下无需传入，AppID 已通过环境变量 `WECHAT_APP_ID` 预配置。** |

> `content`、`content_url`、`file` 三选一提供即可。

### `list_themes` — 列出主题

列出所有可用主题（内置 + 自定义）。

### `register_theme` — 注册主题

注册自定义 CSS 主题。

| 参数 | 类型 | 说明 |
|------|------|------|
| `name` | string | 主题名称 |
| `path` | string | CSS 文件路径或 URL |

### `remove_theme` — 删除主题

删除已注册的自定义主题。

## 文章格式

Markdown 顶部需要包含 frontmatter：

```md
---
title: 文章标题（必填）
cover: /path/to/cover.jpg
author: 作者名
source_url: https://原文地址
---

正文内容...
```

## 环境变量

| 变量 | 说明 |
|------|------|
| `WECHAT_APP_ID` | 微信公众号 AppID |
| `WECHAT_APP_SECRET` | 微信公众号 AppSecret |

> 确保运行本工具的机器 IP 已加入微信公众号后台的 **IP 白名单**。

## Server 模式

适用于本地 IP 不固定或需要团队协作的场景。MCP 将发布请求转发到云端 Server，由 Server 调用微信 API。

```json
{
  "mcpServers": {
    "gzh-mcp": {
      "command": "gzh-mcp",
      "args": ["--server", "https://api.example.com", "--api-key", "your-api-key"]
    }
  }
}
```

## Docker 部署

```bash
docker pull chef-guo/gzh-mcp:latest
```

```json
{
  "mcpServers": {
    "gzh-mcp": {
      "command": "docker",
      "args": [
        "run", "--rm", "-i",
        "-v", "/your/host/file/path:/mnt/host-downloads",
        "-e", "WECHAT_APP_ID=your_app_id",
        "-e", "WECHAT_APP_SECRET=your_app_secret",
        "-e", "HOST_FILE_PATH=/your/host/file/path",
        "chef-guo/gzh-mcp"
      ]
    }
  }
}
```

## 致谢
https://github.com/caol64/wenyan-mcp

## License

Apache License Version 2.0
