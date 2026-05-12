# Visão Geral da Arquitetura

## Arquitetura Medalhão

A Arquitetura Medalhão organiza os dados em camadas progressivas de qualidade, cada uma com responsabilidade bem definida.

```mermaid
flowchart TD
    SB[(Supabase\nPostgreSQL\n11 tabelas)] -->|pg8000\nSession Pooler IPv4| EX

    subgraph EX[" 000 — Extração "]
        direction LR
        PY[Python\npg8000] --> CSV[CSVs\n/Volumes/workspace/\nlanding/dados]
    end

    EX --> BR

    subgraph BR[" 002 — Bronze "]
        direction LR
        RD[spark.read.csv] --> MT[+ metadados\ndata_hora_bronze\nnome_arquivo]
        MT --> DL1[Delta Tables\nbronze.*]
    end

    BR --> SL

    subgraph SL[" 003 — Silver "]
        direction LR
        RN[renomear_colunas_managed\ncd_ → CODIGO_\nnm_ → NOME_] --> DL2[Delta Tables\nsilver.*]
    end

    SL --> GL

    subgraph GL[" 004 — Gold "]
        direction LR
        DIM[Dimensões\ndim_carro\ndim_cliente\ndim_localidade\ndim_tempo] --> FT[Fato\nfato_sinistro]
    end

    style SB fill:#3ecf8e,color:#000
    style DL1 fill:#cd7f32,color:#fff
    style DL2 fill:#c0c0c0,color:#000
    style FT fill:#ffd700,color:#000
```

## Princípios Adotados

- **Managed Tables** — todas as tabelas Delta são gerenciadas pelo Unity Catalog
- **Overwrite + Schema Evolution** — reprocessamento seguro com `overwriteSchema: true`
- **SCD Tipo 1** — dimensões atualizadas via MERGE (sem histórico)
- **Rastreabilidade** — cada registro carrega metadados de origem e timestamp de processamento
