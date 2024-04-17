# Aula 07

O Apache Spark é um framework de processamento de dados em larga escala, projetado para ser rápido e eficiente. Ele oferece suporte a diversas linguagens de programação, sendo PySpark a interface para Python.

Nesta aula, abordaremos alguns conceitos essenciais do Spark, incluindo DataFrames, SparkSession, Lazy Evaluation, Transformações e Ações, além do Spark SQL.

## Preparação do Ambiente

### 1. Docker com Spark

```bash
    cd spyrk-cluster
	docker build -t gbieul/spark-base-hadoop:2.4.6 ./docker/spark-base
	docker build -t gbieul/spark-master-hadoop:2.4.6 ./docker/spark-master
	docker build -t gbieul/spark-worker-hadoop:2.4.6 ./docker/spark-worker
```

```bash
    cd user_data
    wget -q https://archive.apache.org/dist/spark/spark-3.3.0/spark-3.3.0-bin-hadoop2.tgz
    tar xf spark-3.3.0-bin-hadoop2.tgz
```

```bash
    docker-compose up -d
```

No navegador, ao acessar `http://localhost:8088/cluster` você poderá ver informações do cluster, se tudo
estiver conforme o esperado (1), ou rodar `docker exec -it spark-master /bin/bash` para ir diretamente ao
shell do container.

## Acessando serviços no navegador

`http://localhost:8088/cluster` - Resource Manager

`http://localhost:11070` - NameNode WebUI

`http://localhost:8888` - Jupyter Notebook

## PySpark e SparkSession

O PySpark é a interface Python para o Apache Spark. Utilizamos o PySpark para escrever programas Spark em Python.

```python
import os
os.environ["JAVA_HOME"] = "/usr/local/openjdk-8"
os.environ["SPARK_HOME"] = "/user_data/spark-3.3.0-bin-hadoop2"

import findspark
findspark.init('spark-3.3.0-bin-hadoop2')

from pyspark.sql import SparkSession


# Inicializar uma SparkSession
spark = SparkSession.builder.appName("ExemploSpark").getOrCreate()

```

```python
spark = (
    SparkSession.builder.appName("sparksubmit_test_app")
    .config("spark.sql.warehouse.dir", "hdfs:///user/hive/warehouse")
    .config("spark.sql.catalogImplementation", "hive")
    .getOrCreate()
)

```

A `SparkSession` é a entrada principal para a funcionalidade Spark. Ela é usada para criar DataFrames, registrar DataFrames como tabelas temporárias e executar operações Spark.

### Upload do Arquivo

Copie o arquivo `iris.data` para o container:

```bash
docker cp iris.data spark-master:/user_data
docker exec -it spark-master /bin/bash
hadoop fs -mkdir /datasets/iris
hadoop fs -put /user_data/iris.data  /datasets/iris
```

## DataFrames

Os DataFrames são a estrutura de dados principal no Spark, semelhante a tabelas em bancos de dados relacionais. Eles são distribuídos e imutáveis.

```python
import requests
from io import StringIO

df = spark.read.csv("hdfs://spark-master:9000/datasets/iris/iris.data", header=False, inferSchema=True)

df.show()

num_linhas = df.count()
print(f"Número de linhas no DataFrame: {num_linhas}")

# Exibir o esquema do DataFrame
df.printSchema()

# Mostrar as primeiras linhas do DataFrame
df.show()
```

## Lazy Evaluation

O Spark usa a Lazy Evaluation para otimizar o plano de execução. Isso significa que as transformações no DataFrame não são executadas imediatamente; em vez disso, o Spark cria um plano de execução que será otimizado antes da execução real.

```python
# Lazy Evaluation - transformação
df_transformado = df.select("coluna1", "coluna2").filter(df["coluna3"] > 50)
```

## Transformações e Ações

As Transformações são operações aplicadas a um DataFrame para criar um novo DataFrame, enquanto as Ações são operações que retornam um resultado ao driver ou gravam dados em um sistema externo.

```python
# Transformação
df_transformado = df.select("coluna1", "coluna2").filter(df["coluna3"] > 50)

# Ação
resultados = df_transformado.collect()
```

## Spark SQL

O Spark SQL permite executar consultas SQL em DataFrames. Isso simplifica a integração de SQL com código Spark.

```python
# Registrar DataFrame como tabela temporária
df.createOrReplaceTempView("tabela_temporaria")

# Executar consulta SQL no DataFrame
resultado_sql = spark.sql("SELECT coluna1, AVG(coluna2) FROM tabela_temporaria GROUP BY coluna1")
resultado_sql.show()
```

## Exercicio

1. **Carregamento de Dados:**

   - Carregue o conjunto de dados fornecido no formato adequado (por exemplo, CSV) em um DataFrame.

2. **Exploração Inicial:**

   - Exiba as primeiras linhas do DataFrame para entender a estrutura dos dados.
   - Verifique o esquema (schema) do DataFrame para entender os tipos de dados e nomes das colunas.

3. **Transformações:**

   - Realize pelo menos três transformações diferentes no DataFrame. Por exemplo, seleção de colunas, filtragem de dados, ou criação de novas colunas.

4. **Ações:**

   - Execute pelo menos duas ações diferentes no DataFrame. Por exemplo, conte o número total de linhas, calcule a média de uma coluna, ou encontre os valores únicos de uma coluna.

5. **Spark SQL:**

   - Registre o DataFrame como uma tabela temporária.
   - Execute pelo menos duas consultas Spark SQL no DataFrame.
