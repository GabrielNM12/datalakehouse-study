# Apache Spark

## O que é

**Apache Spark** é um framework open-source de processamento de dados distribuído, projetado para ser rápido e de propósito geral. Ele processa grandes volumes de dados em memória, sendo significativamente mais rápido do que o modelo MapReduce do Hadoop.

Spark pode ser usado para processamento em batch, streaming em tempo real, machine learning e consultas SQL — tudo sobre a mesma engine.

---

## Arquitetura

O Spark opera em um modelo **driver/executor**:

```
┌─────────────────────────────────────────┐
│              Driver Program             │
│         (SparkContext / SparkSession)   │
└──────────────────┬──────────────────────┘
                   │
         ┌─────────▼──────────┐
         │   Cluster Manager  │
         │  (local / YARN /   │
         │   Kubernetes)      │
         └──┬──────────────┬──┘
            │              │
    ┌───────▼──────┐  ┌────▼─────────┐
    │  Executor 1  │  │  Executor 2  │
    │  (Tasks)     │  │  (Tasks)     │
    └──────────────┘  └─────────────┘
```

- **Driver**: coordena a aplicação, cria o plano de execução
- **Cluster Manager**: aloca recursos (neste projeto usamos `local[*]`)
- **Executors**: processam as tarefas em paralelo

---

## PySpark

**PySpark** é a API Python do Apache Spark. Permite escrever aplicações Spark usando Python, com acesso a todos os módulos do Spark: SQL, Streaming, MLlib e GraphX.

### SparkSession

O ponto de entrada de qualquer aplicação PySpark é a `SparkSession`:

```python
from pyspark.sql import SparkSession

spark = (
    SparkSession.builder
    .appName("MeuApp")
    .master("local[*]")
    .getOrCreate()
)
```

- `.appName()` — nome da aplicação
- `.master("local[*]")` — executa localmente usando todos os núcleos disponíveis
- `.getOrCreate()` — cria uma nova sessão ou reutiliza uma existente

---

## Como é usado neste projeto

Neste projeto o Spark é a engine central que executa todas as operações. Tanto o Delta Lake quanto o Iceberg são configurados como extensões da `SparkSession`, e todas as operações SQL e de leitura/escrita passam pelo Spark.

### Configuração para Delta Lake

```python
from pyspark.sql import SparkSession
from delta import configure_spark_with_delta_pip

builder = (
    SparkSession.builder
    .appName("DeltaLakeStudy")
    .master("local[*]")
    .config("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension")
    .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog")
)

spark = configure_spark_with_delta_pip(builder).getOrCreate()
```

### Configuração para Apache Iceberg

```python
import os
from pyspark.sql import SparkSession

warehouse = os.path.abspath("tmp/iceberg_warehouse")

spark = (
    SparkSession.builder
    .appName("IcebergStudy")
    .master("local[*]")
    .config("spark.sql.extensions", "org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions")
    .config("spark.sql.catalog.local", "org.apache.iceberg.spark.SparkCatalog")
    .config("spark.sql.catalog.local.type", "hadoop")
    .config("spark.sql.catalog.local.warehouse", warehouse)
    .config("spark.jars.packages", "org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:1.5.0")
    .getOrCreate()
)
```

---

## Principais conceitos

**DataFrame**: estrutura de dados distribuída e imutável, similar a uma tabela SQL. É a principal abstração do Spark SQL.

**Transformações vs Ações**: o Spark é *lazy* — transformações (como `filter`, `select`) só são executadas quando uma ação (como `show`, `write`) é chamada.

**Particionamento**: os dados são divididos em partições distribuídas entre os executors, o que permite o processamento paralelo.

---

## Referências

- [Documentação oficial Apache Spark](https://spark.apache.org/docs/latest/)
- [Documentação PySpark](https://spark.apache.org/docs/latest/api/python/)
- [Canal DataWay BR](https://www.youtube.com/@DataWayBR)
