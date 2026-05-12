# 004 — Gold

Constrói o modelo dimensional Star Schema a partir das tabelas Silver usando MERGE (SCD Tipo 1).

## Dimensões criadas

### dim_carro
JOIN entre `carro`, `modelo` e `marca` para desnormalizar os dados do veículo.

### dim_cliente
Dados diretos da tabela `cliente`.

### dim_localidade
JOIN entre `municipio` e `estado`. O `CODIGO_REGIAO` vem de `estado.CODIGO_REGIAO`.

!!! info
    Não existe tabela `regiao` com dados de região geográfica. O código de região está na tabela `estado`.

### dim_tempo
Gerada via PySpark com `spark.range` cobrindo 2023–2026.

### fato_sinistro
Agregação de sinistros com JOINs nas dimensões via surrogate keys.

## Padrão MERGE

```sql
MERGE INTO gold.dim_xxx AS d
USING origem AS r
ON r.chave_negocio = d.chave_negocio
WHEN MATCHED AND (...) THEN UPDATE SET ...
WHEN NOT MATCHED THEN INSERT ...
```
