# Modelo Dimensional — Camada Gold

## Star Schema

```mermaid
erDiagram
    fato_sinistro {
        date FK_TEMPO
        int FK_LOCALIDADE
        int FK_CARRO
        int FK_CLIENTE
        int QTDE_SINISTRO
    }
    dim_tempo {
        date DATA PK
        int ANO
        int MES
        int DIA
        int TRIMESTRE
        string DIA_SEMANA
    }
    dim_carro {
        bigint SK_CARRO PK
        string PLACA
        string MARCA
        string MODELO
        string COR
        int ANO
        string CHASSI
    }
    dim_cliente {
        bigint SK_CLIENTE PK
        int CODIGO_CLIENTE
        string NOME
        string CPF
        char SEXO
        date DATA_NASCIMENTO
    }
    dim_localidade {
        bigint SK_LOCALIDADE PK
        int CODIGO_MUNICIPIO
        string NOME_MUNICIPIO
        string NOME_ESTADO
        string CODIGO_REGIAO
    }

    fato_sinistro }o--|| dim_tempo : FK_TEMPO
    fato_sinistro }o--|| dim_carro : FK_CARRO
    fato_sinistro }o--|| dim_cliente : FK_CLIENTE
    fato_sinistro }o--|| dim_localidade : FK_LOCALIDADE
```

## Descrição das Tabelas

### `dim_carro`
Dimensão de veículos, desnormalizada com marca e modelo via JOIN da Silver.

- Chave de negócio: `PLACA`
- Surrogate key: `SK_CARRO` (identity bigint)

### `dim_cliente`
Dados cadastrais do segurado.

- Chave de negócio: `CODIGO_CLIENTE`
- Surrogate key: `SK_CLIENTE` (identity bigint)

### `dim_localidade`
Localidade desnormalizada: município + estado + código de região.

- Chave de negócio: `CODIGO_MUNICIPIO`
- Surrogate key: `SK_LOCALIDADE` (identity bigint)

!!! info "Observação sobre Região"
    A tabela `regiao` no banco de origem contém dados de município. O `CODIGO_REGIAO` é extraído diretamente da tabela `estado` (coluna `cd_regiao`). Não existe `NOME_REGIAO` na fonte — apenas o código numérico.

### `dim_tempo`
Calendário gerado via PySpark cobrindo 2023–2026.

### `fato_sinistro`
Fato agregado com contagem de sinistros por data, localidade, veículo e cliente.

## Estratégia SCD

Todas as dimensões usam **SCD Tipo 1** (sobrescrita) implementada via `MERGE INTO`:

```sql
MERGE INTO gold.dim_xxx AS d
USING origem AS r
ON r.chave_negocio = d.chave_negocio
WHEN MATCHED AND (...campos mudaram...) THEN UPDATE SET ...
WHEN NOT MATCHED THEN INSERT ...
```
