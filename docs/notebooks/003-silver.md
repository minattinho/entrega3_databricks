# 003 — Silver

Aplica regras de Data Quality nas tabelas Bronze e persiste na camada Silver com nomes de colunas padronizados.

## Função principal

```python
def renomear_colunas_managed(src_fqn: str, dest_fqn: str):
    df = spark.read.format("delta").table(src_fqn)

    # Renomeia todas as colunas aplicando as regras
    new_cols = [_apply_name_rules(c) for c in df.columns]
    df = df.toDF(*new_cols)

    # Remove colunas antigas de auditoria Bronze
    df = df.drop("DATA_HORA_BRONZE", "NOME_ARQUIVO")

    # Adiciona auditoria Silver
    df = df.withColumn("NOME_ARQUIVO_BRONZE", lit(src_fqn))
           .withColumn("DATA_ARQUIVO_SILVER", current_timestamp())

    df.write \
      .format("delta") \
      .mode("overwrite") \
      .option("overwriteSchema", "true") \
      .saveAsTable(dest_fqn)
```

## Regras de renomeação

| De | Para |
|---|---|
| `cd_estado` | `CODIGO_ESTADO` |
| `nm_municipio` | `NOME_MUNICIPIO` |
| `vl_premio` | `VALOR_PREMIO` |
| `dt_sinistro` | `DATA_SINISTRO` |
| `nr_telefone` | `NUMERO_TELEFONE` |
| `sigla_uf` | `SIGLA_UNIDADE_FEDERATIVA` |
