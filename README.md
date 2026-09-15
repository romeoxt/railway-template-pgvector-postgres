# Deploy and Host pgvector on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/new)

PostgreSQL 17 with the pgvector extension pre-enabled — embeddings, semantic search, and RAG-ready storage in one click.

## About pgvector

[pgvector](https://github.com/pgvector/pgvector) adds vector similarity search to PostgreSQL. Store OpenAI embeddings, run nearest-neighbor queries, and build RAG pipelines without a separate vector database. Pairs naturally with FastAPI, LangChain, or the [RAG API template](https://github.com/romeoxt/railway-template-rag-pgvector).

## About Hosting pgvector on Railway

This template deploys the official `pgvector/pgvector:pg17` image with a persistent volume. The `vector` extension is created automatically on first boot. Railway provides private networking so your app services connect over `${{Postgres.DATABASE_URL}}` without exposing Postgres to the public internet.

## Environment Variables

| Variable | Description | Secret | Example/Notes |
| --- | --- | --- | --- |
| `POSTGRES_USER` | Database superuser | No | `postgres` |
| `POSTGRES_PASSWORD` | Database password | Yes | `${{secret(32)}}` |
| `POSTGRES_DB` | Default database name | No | `railway` |
| `PGDATA` | Data directory (subfolder on volume) | No | `/var/lib/postgresql/data/pgdata` |

Railway auto-generates `DATABASE_URL` for linked services when you reference this Postgres service.

## Deploy and Host

1. Create a new Railway project.
2. Deploy this repo as a **Docker** service named **Postgres pgvector**.
3. Set `POSTGRES_PASSWORD` = `${{secret(32)}}` (and optionally `POSTGRES_USER`, `POSTGRES_DB`).
4. Attach a **volume** at `/var/lib/postgresql/data` and set `PGDATA=/var/lib/postgresql/data/pgdata` (subfolder required on Railway volumes).
5. Enable **TCP proxy** on port **5432** if you need external access; keep HTTP disabled.
6. Deploy. On first run, `init.sql` runs `CREATE EXTENSION vector`.
7. Point your app at `${{Postgres.DATABASE_URL}}` or the private connection string.

Verify the extension:

```sql
SELECT extname, extversion FROM pg_extension WHERE extname = 'vector';
```

If you redeploy onto an existing volume and vector is missing, run manually:

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

## Common Use Cases

- RAG backends storing document embeddings
- Semantic search over product catalogs or docs
- AI agent memory and retrieval layers
- Prototyping vector search before moving to a dedicated DB

## Dependencies for pgvector Hosting

The Railway template includes:

- **Postgres pgvector** — `pgvector/pgvector:pg17` (this repo Dockerfile)
- **Volume** — `/var/lib/postgresql/data` for persistent storage

Optional add-ons in separate templates:

- **PgWeb** — browser SQL client ([postgres-pgweb template](https://github.com/romeoxt/railway-template-postgres-pgweb))
- **RAG API** — ingest + ask endpoints ([rag-pgvector template](https://github.com/romeoxt/railway-template-rag-pgvector))

## Deployment Dependencies

- [pgvector documentation](https://github.com/pgvector/pgvector)
- [pgvector Docker image](https://hub.docker.com/r/pgvector/pgvector)
- [Railway PostgreSQL docs](https://docs.railway.com/databases/postgresql)

## Why Deploy pgvector on Railway?

One Postgres instance with vectors, private networking to your API, and volume-backed persistence — no managed vector DB bill while you validate an MVP.

## Template Content

| Service | Source |
| --- | --- |
| Postgres pgvector | `pgvector/pgvector:pg17` |

## Example vector table

```sql
CREATE TABLE documents (
  id bigserial PRIMARY KEY,
  content text,
  embedding vector(1536)
);

CREATE INDEX ON documents USING hnsw (embedding vector_cosine_ops);
```

## Marketing site

See `website/index.html` for the template landing page.

## Author

romeoxt — herbylegall9@gmail.com

## License

MIT
