# Conexão Supabase

## Por que Session Pooler?

O Databricks Serverless opera exclusivamente em **IPv4**. A conexão direta do Supabase usa IPv6 por padrão, causando timeout.

O **Session Pooler** (PgBouncer) expõe um endpoint IPv4 compatível.

## Como configurar

1. No painel do Supabase: **Project Settings → Database → Connection**
2. Selecionar **Session pooler**
3. Copiar host e user

```python
DB_HOST = "aws-1-us-west-1.pooler.supabase.com"
DB_PORT = 5432
DB_USER = "postgres.<project-ref>"   # formato diferente da conexão direta
```

## Por que pg8000?

| Driver | Tipo | Compatível Serverless |
|---|---|---|
| `psycopg2` | C extension | ❌ SIGABRT |
| `psycopg2-binary` | C binary | ❌ SIGABRT |
| `pg8000` | Pure Python | ✅ |

O `psycopg2` usa extensões C que conflitam com a JVM do Spark no ambiente ARM64 do Serverless, causando abort do processo (exit code 134).
