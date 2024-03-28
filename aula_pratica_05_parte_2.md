# Aula 05 - parte 2

## Roteiro da Aula de HiveQL

### Introdução

Nesta aula, exploraremos o HiveQL, uma linguagem de consulta do Apache Hive que facilita a interação com grandes conjuntos de dados armazenados no Hadoop Distributed File System (HDFS). O objetivo é realizar operações de manipulação e análise de dados na tabela `cars_parquet`.

### 1. Conectar-se ao Hive

Vamos iniciar a aula conectando-nos ao Hive.

```bash
docker exec -it docker-hive-hive-server-1 /bin/bash
hive
```

### 2. Criar Banco de Dados e Usá-lo

```sql
CREATE DATABASE IF NOT EXISTS mydatabase;
USE mydatabase;
```

### 3. Criar Tabela Hive

```sql
CREATE EXTERNAL TABLE cars_parquet (
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
) STORED AS PARQUET LOCATION '/user/hive/cars';;
```

### 4. Criando tabelas

```sql
CREATE TABLE IF NOT EXISTS additional_info (
    model STRING,
    manufacturer STRING,
    origin STRING
) STORED AS PARQUET;

-- Carregar dados de exemplo
INSERT INTO additional_info VALUES
    ('Mazda RX4', 'Mazda', 'Japan'),
    ('Mazda RX4 Wag', 'Mazda', 'Japan'),
    ('Datsun 710', 'Datsun', 'Japan'),
    ('Hornet 4 Drive', 'AMC', 'USA'),
    ('Hornet Sportabout', 'AMC', 'USA'),
    ('Valiant', 'Plymouth', 'USA'),
    ('Duster 360', 'Plymouth', 'USA'),
    ('Merc 240D', 'Mercedes', 'Germany'),
    ('Merc 230', 'Mercedes', 'Germany'),
    ('Merc 280', 'Mercedes', 'Germany');
```

### 5. Consultas Básicas

#### 5.1. Listar Todas as Linhas

```sql
SELECT * FROM cars_parquet;
```

#### 5.2. Filtrar Dados

```sql
SELECT * FROM cars_parquet WHERE mpg > 20;
```

#### 5.3. Contagem de Linhas

```sql
SELECT COUNT(*) FROM cars_parquet;
```

### 6. Atualizar Dados

Não é suportado naturalmente

### 7. Inserir Novos Dados

```sql
INSERT INTO cars_parquet VALUES ('Toyota Corolla', 30.0, 4, 120.1, 80, 4.1, 2.3, 15.0, 1, 0, 3, 1);
SELECT * FROM cars_parquet WHERE model like 'Toyota Corolla';
```

### 8. Excluir Registros

Não é suportado naturalmente

### 9. Consulta Com Join

```sql
SELECT cars_parquet.model, cars_parquet.mpg, cars_parquet.gear, additional_info.*
FROM cars_parquet
JOIN additional_info
ON cars_parquet.model = additional_info.model;
```

### Exercício Prático - Manipulação de Dados no Hive

1. **Consulta Básica:**

   - Execute uma consulta para obter as 5 primeiras linhas da tabela `cars_parquet`.

2. **Filtragem e Ordenação:**

   - Faça uma consulta para listar os carros que têm um consumo de combustível (`mpg`) superior a 20, ordenando o resultado pelo consumo de combustível em ordem decrescente.

3. **Agregação e Agrupamento:**

   - Calcule a média do peso (`wt`) dos carros para cada número de cilindros (`cyl`). Ordene o resultado pela média do peso em ordem decrescente.

4. **Criação de Nova Tabela:**

   - Crie uma nova tabela chamada `fuel_efficient_cars` que inclua apenas carros com consumo de combustível superior a 25. Utilize a estrutura da tabela `cars_parquet`.

5. **Exploração do Metastore:**
   - Dentro do Hive CLI, execute os seguintes comandos para explorar o Metastore:
     - `SHOW DATABASES;` - Exibir os bancos de dados disponíveis.
     - `USE <seu_banco_de_dados>;` - Acesse um banco de dados específico.
     - `SHOW TABLES;` - Exibir as tabelas presentes no banco de dados atual.
     - `DESCRIBE cars_parquet;` - Obter informações detalhadas sobre a tabela `cars_parquet`.
