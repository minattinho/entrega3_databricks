# Ambiente Databricks

## Configure Environment

No Job (`Jobs & Pipelines → Edit task → Configure Environment`):

```
pg8000
```

Não adicionar `psycopg2`, `psycopg2-binary`, `databricks-sql-connector` ou `-r requirements.txt` sem o arquivo existir.

## Ordem das Tasks no Job

```
001-ambiente → 000-extracao → 002-bronze → 003-silver → 004-gold
```

Cada task deve declarar dependência da anterior para garantir a execução sequencial.

## Compute

- Tipo: **Serverless**
- Base environment: **Standard v5** (Python 3.12, Scala 2.13)
