# Unity Catalog

## Estrutura

```
workspace  (catálogo)
├── landing          ← schema de entrada
│   └── dados/       ← volume com os CSVs brutos
├── bronze           ← Delta tables raw
├── silver           ← Delta tables padronizadas
└── gold             ← Modelo dimensional
```

## Criação (Notebook 001)

```sql
CREATE SCHEMA IF NOT EXISTS workspace.landing;
CREATE VOLUME IF NOT EXISTS workspace.landing.dados;
CREATE SCHEMA IF NOT EXISTS workspace.bronze;
CREATE SCHEMA IF NOT EXISTS workspace.silver;
CREATE SCHEMA IF NOT EXISTS workspace.gold;
```

## Tabelas por Camada

=== "Bronze"
    | Tabela | Colunas extras |
    |---|---|
    | `bronze.apolice` | `data_hora_bronze`, `nome_arquivo` |
    | `bronze.carro` | `data_hora_bronze`, `nome_arquivo` |
    | `bronze.cliente` | `data_hora_bronze`, `nome_arquivo` |
    | `bronze.endereco` | `data_hora_bronze`, `nome_arquivo` |
    | `bronze.estado` | `data_hora_bronze`, `nome_arquivo` |
    | `bronze.marca` | `data_hora_bronze`, `nome_arquivo` |
    | `bronze.modelo` | `data_hora_bronze`, `nome_arquivo` |
    | `bronze.municipio` | `data_hora_bronze`, `nome_arquivo` |
    | `bronze.regiao` | `data_hora_bronze`, `nome_arquivo` |
    | `bronze.sinistro` | `data_hora_bronze`, `nome_arquivo` |
    | `bronze.telefone` | `data_hora_bronze`, `nome_arquivo` |

=== "Silver"
    Mesmas tabelas com colunas padronizadas + `NOME_ARQUIVO_BRONZE` + `DATA_ARQUIVO_SILVER`.

=== "Gold"
    `dim_carro`, `dim_tempo`, `dim_cliente`, `dim_localidade`, `fato_sinistro`

## Limpeza do Ambiente

```sql
DROP SCHEMA IF EXISTS workspace.bronze CASCADE;
DROP SCHEMA IF EXISTS workspace.silver CASCADE;
DROP SCHEMA IF EXISTS workspace.gold CASCADE;
DROP VOLUME IF EXISTS workspace.landing.dados;
DROP SCHEMA IF EXISTS workspace.landing CASCADE;
```
