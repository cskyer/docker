# Docker Compose 服务

本仓库维护一组本地自托管服务的 Docker Compose 配置。每个服务目录相互独立。

## 服务列表

| 目录 | 服务 | 默认端口 | 说明 |
| --- | --- | --- | --- |
| `n8n/` | n8n | `5678` | 工作流自动化 |
| `crawl4ai/` | Crawl4AI | `11235` | 网页爬虫 API |
| `postgresql/` | PostgreSQL | `5432` | 数据库服务，支持初始化多个数据库 |

## 常用命令

在对应服务目录中执行命令，例如：

```bash
cd postgresql
docker compose up -d
docker compose ps
docker compose logs -f
docker compose down
```

提交配置变更前先校验 Compose 配置：

```bash
docker compose config
```

## PostgreSQL

`postgresql/.env` 控制本地数据库配置：

```env
POSTGRES_USER=cskyer
POSTGRES_PASSWORD=...
POSTGRES_DB=teea_portal
POSTGRES_MULTIPLE_DATABASES=teea_portal,teea_novel
POSTGRES_PORT=5432
```

启动 PostgreSQL：

```bash
cd postgresql
docker compose up -d
```

查看已创建的数据库：

```bash
docker exec postgres psql -U cskyer -d teea_portal \
  -c "SELECT datname FROM pg_database ORDER BY datname;"
```

如需手动创建额外数据库：

```bash
docker exec postgres psql -U cskyer -d teea_portal \
  -c "CREATE DATABASE teea_novel;"
```

`postgresql/create-multiple-postgresql-databases.sh` 只会在 PostgreSQL 数据卷首次初始化时运行。创建新 volume 前先确保脚本有执行权限：

```bash
chmod +x postgresql/create-multiple-postgresql-databases.sh
```
