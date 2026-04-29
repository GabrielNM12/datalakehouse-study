# Projeto DataLakehouse

> Estudo prático de **Apache Spark** com **Delta Lake** e **Apache Iceberg** | Arquitetura de Dados | SATC

[![Python](https://img.shields.io/badge/python-3.11-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Poetry](https://img.shields.io/badge/gerenciador-poetry-60A5FA)](https://python-poetry.org/)
[![Apache Spark](https://img.shields.io/badge/spark-3.5.1-E25A1C?logo=apachespark&logoColor=white)](https://spark.apache.org/)
[![Delta Lake](https://img.shields.io/badge/delta_lake-3.2.0-003366?logoColor=white)](https://delta.io/)
[![Apache Iceberg](https://img.shields.io/badge/iceberg-1.3.0-4B8BBE)](https://iceberg.apache.org/)
[![MkDocs](https://img.shields.io/badge/docs-mkdocs-526CFE?logo=materialformkdocs&logoColor=white)](https://GabrielNM12.github.io/datalakehouse-study)

&nbsp; 

## Participantes

| Nome | GitHub |
|---|---|
| Vanessa Ugioni | [@vanessaugioni](https://github.com/vanessaugioni) |
| Gabriel Muller | [@GabrielNM12](https://github.com/GabrielNM12) |
| Bettina da Silva | [@berbett](https://github.com/berbett) |


## Documentação

As explicações sobre as tecnologias utilizadas estão disponíveis no MkDocs do projeto:

🔗 **https://GabrielNM12.github.io/datalakehouse-study**

&nbsp; 

## Estrutura do Projeto

```
datalakehouse-study/
├── notebooks/
│   ├── delta_lake.ipynb      # Notebook Delta Lake
│   └── iceberg.ipynb         # Notebook Apache Iceberg
├── docs/                     # Fonte do MkDocs
├── warehouse/                # Gerado automaticamente pelo Spark
├── pyproject.toml            # Dependências gerenciadas pelo Poetry
├── poetry.lock
└── README.md
```


## Pré-requisitos

Instale as ferramentas abaixo antes de prosseguir:

| Ferramenta | Versão | Link |
|---|---|---|
| Java (JDK) | **17** | [adoptium.net](https://adoptium.net/temurin/releases/?version=17) |
| Python | **3.11** | [python.org](https://www.python.org/downloads/) |
| Poetry | **latest** | [python-poetry.org](https://python-poetry.org/docs/#installation) |

> ⚠️ O Apache Spark **não é compatível com Java 21+**. Use obrigatoriamente o **JDK 17**.

### Verificar instalações

```bash
java -version      # openjdk version "17.x.x"
python --version   # Python 3.11.x
poetry --version   # Poetry (version x.x.x)
```

### Configurar JAVA_HOME

**Linux / macOS**: adicione ao `~/.bashrc` ou `~/.zshrc` e reinicie o terminal:

```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
```

**Windows**: execute no PowerShell como Administrador:

```powershell
[System.Environment]::SetEnvironmentVariable(
  "JAVA_HOME",
  "C:\Program Files\Eclipse Adoptium\jdk-17.x.x.x-hotspot",
  "Machine"
)
```

> Após configurar, feche e reabra o terminal. Verifique com `echo $JAVA_HOME` (Linux/macOS) ou `echo %JAVA_HOME%` (Windows).

&nbsp; 

## Instalação

### 1. Clonar o repositório

```bash
git clone https://github.com/GabrielNM12/datalakehouse-study.git
cd datalakehouse-study
```

### 2. Criar o ambiente virtual dentro do projeto

```bash
poetry config virtualenvs.in-project true
```

### 3. Instalar as dependências

```bash
poetry install
```

> Isso cria a pasta `.venv/` com todas as dependências do `pyproject.toml`. Requer conexão com a internet — o Spark também baixará pacotes Maven na primeira execução dos notebooks.

&nbsp; 
## Dependências

Versões definidas no `pyproject.toml`:

```toml
[tool.poetry.dependencies]
python          = "^3.11"
pyspark         = "3.5.1"
delta-spark     = "3.2.0"
jupyterlab      = "^4.0"
mkdocs-material = "^9.0"
```

> O pacote Iceberg (`org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:1.3.0`) é baixado via Maven pelo Spark na primeira execução do notebook `iceberg.ipynb`.

&nbsp; 

##  Executar os Notebooks

### Via JupyterLab

```bash
poetry run jupyter lab
```

Acesse **http://localhost:8888** e abra os notebooks na ordem:

| Notebook | Descrição |
|---|---|
| `notebooks/delta_lake.ipynb` | Delta Lake com Apache Spark |
| `notebooks/iceberg.ipynb` | Apache Iceberg com Apache Spark |

### Via VS Code

1. Instale a extensão [Jupyter](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter)
2. Abra a pasta `datalakehouse-study` no VS Code
3. Abra um dos notebooks em `notebooks/`
4. Clique em **Select Kernel** → **Python Environments** → selecione `.venv`
5. Execute as células na ordem com `Shift + Enter`

> Em ambos os casos, execute as células **uma a uma**. A célula de configuração da SparkSession pode levar alguns segundos na primeira execução.

&nbsp; 

## 📝 Referências

- 📺 [Canal DataWay BR](https://www.youtube.com/@DataWayBR)
- 💻 [jlsilva01/spark-delta](https://github.com/jlsilva01/spark-delta)
- 💻 [jlsilva01/spark-iceberg](https://github.com/jlsilva01/spark-iceberg)
- 📘 [Documentação Delta Lake](https://docs.delta.io/)
- 📘 [Documentação Apache Iceberg](https://iceberg.apache.org/docs/latest/)
- 📘 [Documentação PySpark](https://spark.apache.org/docs/latest/api/python/)
- 📘 [Documentação Poetry](https://python-poetry.org/docs/)

---
&nbsp; 

<div align="center">
  <sub>Desenvolvido para a disciplina de Arquitetura de Dados — SATC</sub>
</div>
