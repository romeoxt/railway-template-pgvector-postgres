# Railway Template Composer Setup

## Marketplace listing

- **Title:** Deploy and Host pgvector on Railway
- **Short description:** PostgreSQL 17 with pgvector pre-enabled — embeddings, semantic search, and RAG-ready storage.
- **Category:** Storage
- **Overview:** paste `README.md`

## Services

| Service | Source | Volume | Public HTTP |
| --- | --- | --- | --- |
| Postgres pgvector | GitHub repo (Dockerfile → `pgvector/pgvector:pg17`) | `/var/lib/postgresql/data` | No (TCP 5432) |

## Variables — Postgres pgvector

| Variable | Value | Secret | Description |
| --- | --- | --- | --- |
| `POSTGRES_USER` | `postgres` | No | Superuser name |
| `POSTGRES_PASSWORD` | `${{secret(32)}}` | Yes | Database password |
| `POSTGRES_DB` | `railway` | No | Default database |
| `PGDATA` | `/var/lib/postgresql/data/pgdata` | No | Subfolder on volume mount (not mount root) |

## Settings — Postgres pgvector

- Enable **TCP proxy** on port 5432 for external clients
- Attach volume before first deploy so `init.sql` creates the extension
- No HTTP healthcheck — database service uses TCP
