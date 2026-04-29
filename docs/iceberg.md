# Apache Iceberg

## O que é

**Apache Iceberg** é um formato de tabela aberta de alto desempenho, criado para grandes volumes de dados analíticos. Assim como o Delta Lake, ele traz transações ACID e controle de versão para o Data Lake — mas com foco em portabilidade e interoperabilidade entre múltiplas engines (Spark, Flink, Trino, Hive).

---

## Principais Recursos

| Recurso | Descrição |
|---|---|
| **Transações ACID** | Leituras e escritas consistentes mesmo com múltiplos processos |
| **Schema Evolution** | Adicionar, renomear ou remover colunas sem reescrever os dados |
| **Partition Evolution** | Alterar a estratégia de particionamento sem migrar dados |
| **Time Travel** | Consultar snapshots anteriores da tabela |
| **Catálogo** | Gerencia metadados de forma independente da engine |

---

## Arquitetura

O Iceberg organiza uma tabela em três camadas:

```
Catálogo (catalog)
    └── Metadata Layer
        ├── metadata.json          ← estado atual da tabela
        ├── snap-xxxxx.avro        ← lista de snapshots
        └── manifest-xxxxx.avro   ← lista de arquivos de dados
              └── Data Layer
                  └── part-00000.parquet
```

Essa separação entre metadados e dados permite operações atômicas e consultas a versões antigas sem duplicar arquivos.

---

## Implementação no Projeto

### 1. Configurar a SparkSession com Iceberg

```python
import os
from pyspark.sql import SparkSession
from pyspark.sql import functions as F

warehouse = os.path.abspath("tmp/iceberg_warehouse")
os.makedirs(warehouse, exist_ok=True)

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

spark.sql("CREATE NAMESPACE IF NOT EXISTS local.default")
```

> O pacote Maven `iceberg-spark-runtime` é baixado automaticamente na primeira execução. Requer conexão com a internet.

### 2. CREATE TABLE — Criar a tabela Iceberg

```python
spark.sql("""
    CREATE TABLE IF NOT EXISTS local.default.iceberg_events (
      id INT,
      name STRING,
      event_date DATE
    )
    USING iceberg
""")
```

### 3. INSERT — Inserir dados na tabela

```python
data = [
    (1, "Alice", "2024-04-01"),
    (2, "Bob",   "2024-04-02"),
    (3, "Carol", "2024-04-03")
]

df = spark.createDataFrame(data, ["id", "name", "event_date"]) \
    .withColumn("event_date", F.to_date("event_date"))

df.write.format("iceberg").mode("append").save("local.default.iceberg_events")

spark.sql("SELECT * FROM local.default.iceberg_events").show()
```

Resultado:

```
+---+-----+----------+
| id| name|event_date|
+---+-----+----------+
|  1|Alice|2024-04-01|
|  2|  Bob|2024-04-02|
|  3|Carol|2024-04-03|
+---+-----+----------+
```

### 4. Schema Evolution — Adicionar uma coluna

Uma das grandes vantagens do Iceberg é a **evolução de schema sem reescrever dados**. Basta um `ALTER TABLE`:

```python
spark.sql("ALTER TABLE local.default.iceberg_events ADD COLUMN country STRING")
```

Inserindo um registro com o novo campo:

```python
new_data = [(4, "Diego", "2024-04-04", "BR")]
df2 = spark.createDataFrame(new_data, ["id", "name", "event_date", "country"]) \
    .withColumn("event_date", F.to_date("event_date"))

df2.write.format("iceberg").mode("append").save("local.default.iceberg_events")

spark.sql("SELECT * FROM local.default.iceberg_events").show()
```

Resultado — registros antigos recebem `null` na nova coluna automaticamente:

```
+---+-----+----------+-------+
| id| name|event_date|country|
+---+-----+----------+-------+
|  1|Alice|2024-04-01|   null|
|  2|  Bob|2024-04-02|   null|
|  3|Carol|2024-04-03|   null|
|  4|Diego|2024-04-04|     BR|
+---+-----+----------+-------+
```

Verificar o schema atualizado:

```python
spark.sql("DESCRIBE local.default.iceberg_events").show(truncate=False)
```

### 5. DELETE — Remover um registro

```python
spark.sql("DELETE FROM local.default.iceberg_events WHERE id = 2")
spark.sql("SELECT * FROM local.default.iceberg_events").show()
```

### 6. Time Travel — Consultar snapshot anterior

```python
spark.sql("""
    SELECT * FROM local.default.iceberg_events
    VERSION AS OF 0
""").show()
```

---

## Delta Lake vs Apache Iceberg

| | Delta Lake | Apache Iceberg |
|---|---|---|
| **Criado por** | Databricks | Netflix / Apple |
| **Schema Evolution** | ✅ | ✅ |
| **Time Travel** | ✅ por versão ou timestamp | ✅ por snapshot ID ou timestamp |
| **Partition Evolution** | ❌ | ✅ |
| **Interoperabilidade** | Principalmente Spark | Spark, Flink, Trino, Hive |
| **Formato de metadados** | JSON (Delta Log) | Avro + JSON |

---

## Referências

- [Documentação oficial Apache Iceberg](https://iceberg.apache.org/docs/latest/)
- [Canal DataWay BR](https://www.youtube.com/@DataWayBR)
- [jlsilva01/spark-iceberg](https://github.com/jlsilva01/spark-iceberg)
