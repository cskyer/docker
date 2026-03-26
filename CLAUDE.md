# CLAUDE.md

本文件为 Claude Code (claude.ai/code) 在此仓库中工作时提供指导。

## 概述

本仓库是一组用于自托管服务的 Docker Compose 配置集合。每个服务都有独立的子目录，其中包含 `compose.yaml`。

## 常用命令

```bash
# 启动服务（在对应子目录中执行）
docker compose up -d

# 停止服务
docker compose down

# 查看日志
docker compose logs -f

# 拉取最新镜像
docker compose pull
```

## 目录结构

每个顶层子目录代表一个自托管服务，各服务相互独立，每个目录下均包含一个 `compose.yaml`。

| 目录          | 服务       | 端口    | 说明             |
|-------------|----------|-------|----------------|
| `n8n/`      | n8n      | 5678  | 工作流自动化平台       |
| `crawl4ai/` | Crawl4AI | 11235 | AI 驱动的网页爬虫 API |