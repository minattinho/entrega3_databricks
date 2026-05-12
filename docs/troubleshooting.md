# Troubleshooting

## SIGABRT — exit code 134

**Sintoma:** `Fatal Python error: Aborted` / `The Python process exited with exit code 134`

**Causa:** `psycopg2` ou `psycopg2-binary` sendo importado no Databricks Serverless ARM64.

**Solução:** Substituir por `pg8000` (pure Python, sem extensões C).

---

## Connection timed out — porta 5432

**Sintoma:** `connection to server at "xxx.supabase.co" port 5432 failed: Connection timed out`

**Causa:** Databricks Serverless só tem IPv4; conexão direta do Supabase usa IPv6.

**Solução:** Usar **Session Pooler** do Supabase.

```python
DB_HOST = "aws-X-us-west-X.pooler.supabase.com"
DB_USER = "postgres.<project-ref>"
```

---

## DELTA_METADATA_MISMATCH

**Sintoma:** `A metadata mismatch was detected when writing to the Delta table. SQLSTATE: 42KDG`

**Causa:** Schema da tabela Delta diverge do DataFrame sendo escrito.

**Solução:** Adicionar `.option("overwriteSchema", "true")` no write.

```python
df.write.format("delta").mode("overwrite") \
  .option("overwriteSchema", "true") \
  .saveAsTable("bronze.tabela")
```

---

## PATH_NOT_FOUND no Bronze

**Sintoma:** `Path does not exist: dbfs:/Volumes/workspace/landing/dados/apolice.csv`

**Causa:** Notebook de extração (000) não foi executado antes do Bronze, ou o Volume foi limpo.

**Solução:** Executar o notebook 000 antes do 002, sempre que o pipeline rodar do início.

---

## Cannot save file into non-existent directory

**Sintoma:** `Cannot save file into a non-existent directory: '/Volumes/workspace/landing/dados'`

**Causa:** O Volume existe no Unity Catalog mas o diretório físico não foi inicializado.

**Solução:** Adicionar `dbutils.fs.mkdirs(VOLUME_PATH)` antes do loop de extração no notebook 000.

```python
dbutils.fs.mkdirs(VOLUME_PATH)
```

---

## UNRESOLVED_COLUMN — r.codigo_regiao

**Sintoma:** `A column, variable, or function parameter with name r.codigo_regiao cannot be resolved`

**Causa:** A tabela `regiao` no banco de origem não contém coluna `codigo_regiao` — ela espelha dados de `municipio`. O código de região está em `estado.cd_regiao`.

**Solução:** Remover o JOIN com `regiao` e usar `estado.CODIGO_REGIAO` diretamente.

```sql
WITH localidade_relacional AS (
    SELECT m.CODIGO_MUNICIPIO,
           m.NOME_MUNICIPIO,
           e.NOME_ESTADO,
           e.CODIGO_REGIAO        -- ← vem de estado, não de regiao
    FROM municipio m
    INNER JOIN estado e ON m.CODIGO_ESTADO = e.CODIGO_ESTADO
)
```

---

## SyntaxError: incomplete input

**Sintoma:** Erro de sintaxe Python na última linha de uma célula.

**Causa:** Código foi colado parcialmente — o `except` ou o fechamento do bloco foi cortado.

**Solução:** Garantir que toda a célula está completa antes de executar. Copiar o bloco inteiro de uma vez.
