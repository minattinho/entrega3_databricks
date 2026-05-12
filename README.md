# Arquitetura Medalhão

Pipeline de dados com **Databricks + Supabase** seguindo a Arquitetura Medalhão (Bronze → Silver → Gold).

![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python)
![Databricks](https://img.shields.io/badge/Databricks-Serverless-red?logo=databricks)
![Delta Lake](https://img.shields.io/badge/Delta_Lake-3.0-blue)
![Supabase](https://img.shields.io/badge/Supabase-PostgreSQL-3ecf8e?logo=supabase)

📄 **Para detalhes sobre a arquitetura, modelo dimensional e cada notebook, acesse a documentação completa:** [minattinho.github.io/entrega3_databricks](https://minattinho.github.io/entrega3_databricks/)

---

## Pré-requisitos

- Workspace Databricks com Unity Catalog habilitado
- Projeto no Supabase com as tabelas do domínio
- Git instalado localmente

---

## Configuração do Ambiente

### 1. Clone o repositório

```bash
git clone https://github.com/minattinho/entrega3_databricks.git
cd entrega3_databricks
```

### 2. Importe os notebooks no Databricks

No Databricks, vá em **Workspace → Import** e importe os arquivos `.dbc` da pasta `notebooks/` na seguinte ordem:

```
001 - Preparando Ambiente
000 - Extração (Supabase)
002 - Bronze
003 - Silver
004 - Gold
005 - Destruindo Ambiente  ← apenas para reset
```

### 3. Configure o ambiente do Job

No Databricks, vá em **Jobs & Pipelines → Edit task → Configure Environment** e adicione a dependência:

```
pg8000
```

> ⚠️ Não usar `psycopg2` ou `psycopg2-binary` — causam crash (SIGABRT) no Serverless.

### 4. Configure as credenciais do Supabase

No notebook `000 - Extração`, preencha as variáveis:

```python
DB_HOST     = "aws-1-us-west-1.pooler.supabase.com"  # Session Pooler
DB_PORT     = 5432
DB_NAME     = "postgres"
DB_USER     = "postgres.<seu-project-ref>"
DB_PASSWORD = "<sua-senha>"
VOLUME_PATH = "/Volumes/workspace/landing/dados"
```

> ⚠️ Use o **Session Pooler** do Supabase, não a conexão direta. O Serverless só suporta IPv4.
>
> Para encontrar o host do pooler: **Supabase → Project Settings → Database → Session pooler**

---

## Executando o Pipeline

Execute os notebooks **nessa ordem** no Databricks:

```
001 → 000 → 002 → 003 → 004
```

| Passo | Notebook | O que faz |
|---|---|---|
| 1 | `001 - Ambiente` | Cria schemas e volumes no Unity Catalog |
| 2 | `000 - Extração` | Extrai dados do Supabase e salva como CSV |
| 3 | `002 - Bronze` | Converte CSVs em Delta Lake |
| 4 | `003 - Silver` | Padroniza colunas e aplica Data Quality |
| 5 | `004 - Gold` | Gera o modelo dimensional (Star Schema) |

> O notebook `001` só precisa rodar **uma vez** por ambiente. Nas execuções seguintes, comece pelo `000`.

---