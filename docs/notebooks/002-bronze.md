# 002 — Bronze

Lê os CSVs do Volume, adiciona metadados de auditoria e persiste como tabelas Delta na camada Bronze.

## Passos

1. Listar arquivos em `/Volumes/workspace/landing/dados/`
2. Ler cada CSV com `spark.read.csv` e `inferSchema=true`
3. Adicionar colunas de metadado com `withColumn`
4. Salvar como Delta Managed Table em `bronze.*`

## Metadados adicionados

```python
df = df.withColumn("data_hora_bronze", current_timestamp())
       .withColumn("nome_arquivo", lit("apolice.csv"))
```

## Escrita Delta

```python
df.write \
  .format("delta") \
  .mode("overwrite") \
  .option("overwriteSchema", "true") \
  .saveAsTable("bronze.apolice")
```

!!! warning "overwriteSchema obrigatório"
    Sempre usar `.option("overwriteSchema", "true")` para evitar `DELTA_METADATA_MISMATCH` quando o schema da fonte mudar.
