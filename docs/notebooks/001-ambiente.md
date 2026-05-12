# 001 — Preparando Ambiente

Cria toda a infraestrutura necessária no Unity Catalog antes de iniciar o pipeline.

## O que cria

```sql
-- Schema de entrada (landing zone)
CREATE SCHEMA IF NOT EXISTS workspace.landing;

-- Volume para armazenar os CSVs brutos
CREATE VOLUME IF NOT EXISTS workspace.landing.dados;

-- Schemas das camadas Medalhão
CREATE SCHEMA IF NOT EXISTS workspace.bronze;
CREATE SCHEMA IF NOT EXISTS workspace.silver;
CREATE SCHEMA IF NOT EXISTS workspace.gold;
```

!!! tip
    Este notebook precisa rodar **uma única vez** por ambiente. Nas execuções subsequentes do pipeline, pode ser pulado.
