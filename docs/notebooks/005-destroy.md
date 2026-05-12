# 005 — Destruindo Ambiente

Remove toda a infraestrutura criada. Use apenas para reset completo do ambiente de testes.

```sql
DROP SCHEMA IF EXISTS workspace.bronze CASCADE;
DROP SCHEMA IF EXISTS workspace.silver CASCADE;
DROP SCHEMA IF EXISTS workspace.gold CASCADE;
DROP VOLUME IF EXISTS workspace.landing.dados;
DROP SCHEMA IF EXISTS workspace.landing CASCADE;
```

!!! danger "Irreversível"
    Este notebook apaga **todos** os dados de todas as camadas. Não execute em ambiente de produção.
