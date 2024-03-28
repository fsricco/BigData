# Aula 05 - parte 1

# Aula 5: Introdução ao Hadoop - Parte 1

## Objetivo

Familiarizar os alunos com operações básicas no hive, incluindo o armazenamento de datasets em diferentes formatos, a criação de tabelas internas e externas, a exploração do metastore e a configuração de múltiplos datanodes para computação distribuída no Hive.

## 1. Armazenamento de Datasets

### 1.1 Salvar Datasets em Text File

```bash
docker cp countries.csv docker-hive-datanode-1:/hadoop-data
docker exec -it docker-hive-datanode-1 /bin/bash
hadoop fs -mkdir /user/hive/countries
hadoop fs -put /hadoop-data/countries.csv  /user/hive/countries
```

countries.csv

### 1.2 Salvar Datasets em ORC

```bash
docker cp orc-file-11-format.orc docker-hive-datanode-1:/hadoop-data
docker exec -it docker-hive-datanode-1 /bin/bash
hadoop fs -mkdir /user/hive/orc-file-11-format
hadoop fs -put /hadoop-data/orc-file-11-format.orc /user/hive/orc-file-11-format
```

### 1.3 Salvar Datasets em Parquet

```bash
docker cp cars.parquet docker-hive-datanode-1:/hadoop-data
docker exec -it docker-hive-datanode-1 /bin/bash
hadoop fs -mkdir /user/hive/cars
hadoop fs -put /hadoop-data/cars.parquet /user/hive/cars
```

## 2. Criação de Tabelas

### 2.1 Criar Tabela Interna

Os dados associados a tabelas internas são totalmente gerenciados pelo Hive.

Quando uma tabela interna é descartada (usando DROP TABLE), o Hive exclui tanto a definição da tabela quanto os dados associados.

As tabelas internas são úteis quando você deseja que o Hive seja responsável pela gestão completa dos dados, incluindo a exclusão de dados quando a tabela é removida.

```bash
docker exec -it docker-hive-hive-server-1 /bin/bash
hive -e "CREATE TABLE internal_table (id INT, name STRING, salary DOUBLE) STORED AS TEXTFILE;"
```

### 2.2 Criar Tabela Externa

As tabelas externas permitem ao Hive acessar dados que estão localizados fora do seu controle direto. Os dados podem residir em locais como sistemas de arquivos locais, HDFS ou armazenamento em nuvem.

Ao contrário das tabelas internas, ao descartar uma tabela externa, o Hive remove apenas a definição da tabela, não os dados subjacentes. Os dados continuam existindo no local original.

As tabelas externas são úteis quando você deseja que os dados persistam, mesmo que a tabela Hive associada seja descartada.

```bash
docker exec -it docker-hive-hive-server-1 /bin/bash

hive -e "CREATE EXTERNAL TABLE cars_parquet (
    model STRING,
    mpg DOUBLE,
    cyl INT,
    disp DOUBLE,
    hp INT,
    drat DOUBLE,
    wt DOUBLE,
    qsec DOUBLE,
    vs INT,
    am INT,
    gear INT,
    carb INT
) STORED AS PARQUET LOCATION '/user/hive/cars';"


```

## 3. Metastore

### 3.1 Explorar Metastore

```bash
docker exec -it docker-hive-hive-server-1 /bin/bash
hive
```

Dentro do Hive CLI, execute:

```sql
SHOW DATABASES;
USE default;
SHOW TABLES;
DESCRIBE internal_table;
DESCRIBE EXTENDED cars_parquet;
```

## 4. Dashboard do Namenode

Acesse a dashboard do Namenode em http://localhost:50070 para monitorar o estado do Hadoop Cluster.

# Exercício

## Objetivo

Realizar operações avançadas no Hive, incluindo o download de um dataset externo (do Kaggle), a ingestão no HDFS, e a criação de tabelas interna e externa no Hive com base nesses dados.

## Tarefas

### 1. Download de Dataset do Kaggle

1.1. Acesse o [Kaggle](https://www.kaggle.com/) e encontre um dataset de sua escolha.

1.2. Baixe o dataset em seu ambiente local.

### 2. Transferência para o Container

2.1. Utilize o comando `docker cp` para transferir o arquivo do seu ambiente local para o container do Hadoop (por exemplo, `docker-hive-datanode-1`).

### 3. Ingestão no HDFS

3.1. Acesse o container do Hadoop.

3.2. Utilize os comandos Hadoop para criar um diretório no HDFS e carregar o dataset nele.

### 4. Criação de Tabelas

4.1. Acesse o container do Hive Server.

4.2. Execute o comando HiveQL para criar uma tabela interna chamada `external_table` com colunas e tipos de dados baseados no dataset que você baixou.

4.3. Execute o comando HiveQL para criar uma tabela externa chamada `internal_table` com colunas e tipos de dados baseados no mesmo dataset.

### 5. Consultas no Hive

5.1. Utilizando o Hive CLI ou outra interface de sua preferência, execute consultas SQL para listar registros e realizar análises exploratórias nas tabelas `external_table` e `internal_table`.
