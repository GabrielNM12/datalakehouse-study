# Projeto DataLakehouse Study

> Estudo prático de **Apache Spark** com **Delta Lake** e **Apache Iceberg** | Arquitetura de Dados | SATC

---

## Sobre o Projeto

Este projeto explora na prática dois dos principais formatos de tabela aberta para arquiteturas **Data Lakehouse**: **Delta Lake** e **Apache Iceberg**, ambos integrados ao **Apache Spark** via PySpark.

O objetivo é demonstrar como esses formatos habilitam operações transacionais (INSERT, UPDATE, DELETE), controle de versão e evolução de schema diretamente sobre um Data Lake — capacidades que antes eram exclusivas de bancos de dados relacionais.

---

## Cenário dos Dados

### Delta Lake — Tabela de Funcionários

O notebook Delta Lake trabalha com uma tabela de **funcionários**, contendo id, nome e salário. As operações demonstradas são criação, leitura, atualização de salário e time travel para consultar versões anteriores dos dados.

| Coluna | Tipo | Descrição |
|---|---|---|
| `id` | INT | Identificador único |
| `name` | STRING | Nome do funcionário |
| `salary` | INT | Salário |

### Apache Iceberg — Tabela de Eventos

O notebook Iceberg trabalha com uma tabela de **eventos**, contendo id, nome, data do evento e, após evolução de schema, o país. As operações demonstradas são criação, inserção, leitura e adição de coluna em tempo real.

| Coluna | Tipo | Descrição |
|---|---|---|
| `id` | INT | Identificador único |
| `name` | STRING | Nome do participante |
| `event_date` | DATE | Data do evento |
| `country` | STRING | País *(adicionado via schema evolution)* |

---

## Modelo ER

```
┌──────────────────────┐        ┌──────────────────────────┐
│   delta_demo         │        │   iceberg_events          │
│──────────────────────│        │──────────────────────────│
│ id         INT (PK)  │        │ id          INT (PK)      │
│ name       STRING    │        │ name        STRING        │
│ salary     INT       │        │ event_date  DATE          │
└──────────────────────┘        │ country     STRING        │
                                └──────────────────────────┘
```

---

## Tecnologias Utilizadas

| Tecnologia | Versão |
|---|---|
| Apache Spark (PySpark) | 3.5.1 |
| Delta Lake | 3.2.0 |
| Apache Iceberg | 1.5.0 |
| Python | 3.11 |
| Poetry | latest |
| JupyterLab | 4.x |

---

## Estrutura dos Notebooks

| Notebook | Operações |
|---|---|
| `notebooks/delta_lake.ipynb` | CREATE, INSERT, UPDATE, Time Travel |
| `notebooks/iceberg.ipynb` | CREATE, INSERT, Schema Evolution |

---

## Integrantes

| Nome | GitHub |
|---|---|
| Vanessa Ugioni | [@vanessaugioni](https://github.com/vanessaugioni) |
| Gabriel Muller | [@GabrielNM12](https://github.com/GabrielNM12) |
| Bettina da Silva | [@berbett](https://github.com/berbett) |

---

## Repositório

🔗 [github.com/vanessaugioni/datalakehouse-study](https://github.com/vanessaugioni/datalakehouse-study)
