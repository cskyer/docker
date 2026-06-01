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

# 校验 compose 配置
docker compose config
```

## 目录结构

每个顶层子目录代表一个自托管服务，各服务相互独立，每个目录下均包含一个 `compose.yaml`。

| 目录             | 服务         | 端口    | 说明             |
|----------------|------------|-------|----------------|
| `n8n/`         | n8n        | 5678  | 工作流自动化平台       |
| `crawl4ai/`    | Crawl4AI   | 11235 | AI 驱动的网页爬虫 API |
| `postgresql/`  | PostgreSQL | 5432  | 关系型数据库         |

## PostgreSQL 说明

`postgresql/.env` 定义本地数据库配置，当前常用值包括：

- `POSTGRES_USER=cskyer`
- `POSTGRES_DB=teea_portal`
- `POSTGRES_MULTIPLE_DATABASES=teea_portal,teea_novel`
- `POSTGRES_PORT=5432`

启动 PostgreSQL：

```bash
cd postgresql
docker compose up -d
```

检查容器状态和数据库列表：

```bash
docker compose ps
docker exec postgres psql -U cskyer -d teea_portal \
  -c "SELECT datname FROM pg_database ORDER BY datname;"
```

如果初始化脚本未执行，可手动创建额外数据库：

```bash
docker exec postgres psql -U cskyer -d teea_portal \
  -c "CREATE DATABASE teea_novel;"
```

注意：`create-multiple-postgresql-databases.sh` 只会在 PostgreSQL 数据卷首次初始化时执行。创建新 volume 前需要确保脚本有执行权限：

```bash
chmod +x postgresql/create-multiple-postgresql-databases.sh
```
