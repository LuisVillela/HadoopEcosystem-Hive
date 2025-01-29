# Instrucciones para levantar el ecosistema de Hadoop + Hive

---

Este proyecto se basa en las instrucciones de este enlace:
[Big-Data-Workshops-Hadoop-Hive](https://jcarriolaa.github.io/Big-Data-Workshops-Hadoop-Hive/),
pero, como es de esperar, no está del todo completo. Se realizaron varios ajustes en los archivos,
tanto en el `docker-compose.yml` como en los archivos de configuración, por lo que este
proyecto ya contiene esos archivos optimizados para que funcione correctamente con los siguientes pasos.

---

## 1) Levantar el Docker

Ejecutar el siguiente comando:
```sh
docker-compose up -d
```

### Resultado esperado:
```sh
[+] Running 13/13
 ✔ hive-server Pulled
[+] Running 5/5
 ✔ Network big-data-workshops-hadoop-hive_default  Created
 ✔ Container namenode                              Started
 ✔ Container datanode2                             Started
 ✔ Container datanode                              Started
 ✔ Container hive-server                           Started  
```

---

## 2) Verificar los datasets en HDFS

Ejecutar:
```sh
docker exec -it hive-server hdfs dfs -ls /datasets
```

### Resultado esperado:
```sh
Found 3 items
-rw-r--r--   1 root root        150 2025-01-28 23:20 /datasets/employees.csv
-rw-r--r--   1 root root        116 2025-01-28 23:20 /datasets/sales.csv
-rw-r--r--   1 root root        114 2025-01-28 23:20 /datasets/word_count.txt
```

---

## 3) Conectarse a HiveServer2

Ejecutar:
```sh
docker exec -it hive-server beeline -u jdbc:hive2://localhost:10000
```

### Resultado esperado:
```sh
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/opt/hive/lib/log4j-slf4j-impl-2.6.2.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/opt/hadoop-2.7.4/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]
Connecting to jdbc:hive2://localhost:10000
Connected to: Apache Hive (version 2.3.2)
Driver: Hive JDBC (version 2.3.2)
Transaction isolation: TRANSACTION_REPEATABLE_READ
Beeline version 2.3.2 by Apache Hive
0: jdbc:hive2://localhost:10000>
```

## ¡Listo para crear tablas y hacer consultas!

Una vez completados estos tres pasos, ya puedes crear las tablas y ejecutar consultas en Hive.

---

## 4) Crear las tablas

```
CREATE TABLE word_count (word STRING)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'hdfs://namenode:9000/datasets';

CREATE TABLE sales (id INT, product STRING, price FLOAT, quantity INT)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION 'hdfs://namenode:9000/datasets';

CREATE TABLE employees (id INT, name STRING, department STRING, salary FLOAT)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION 'hdfs://namenode:9000/datasets';
```

### **Importante:**

Al crear las tablas en Hive, **es necesario** utilizar la siguiente Location para los datos en HDFS (y **no** la que indican las instrucciones originales de `Big-Data-Workshops-Hadoop-Hive`) usa los queries de este readme.

```sql
LOCATION 'hdfs://namenode:9000/datasets';
```

Con esto, evitarás problemas de acceso y asegurarte de que las tablas se creen correctamente en la ubicación adecuada.

---


## 5)  Subir un arhcivo para asegurar la fucnioanlidad del Clúster Hadoop Multi-Node
```
 docker exec -it namenode hdfs fsck /example.txt -files -blocks -locations
```

### Resultado esperado:
```sh
Connecting to namenode via http://namenode:9870/fsck?ugi=root&files=1&blocks=1&locations=1&path=%2Fexample.txt
FSCK started by root (auth:SIMPLE) from /172.18.0.2 for path /example.txt at Wed Jan 29 00:36:16 UTC 2025

/example.txt 71 bytes, replicated: replication=1, 1 block(s):  OK
0. BP-199218365-172.18.0.2-1737680550051:blk_1073741828_1004 len=71 Live_repl=1  [DatanodeInfoWithStorage[172.18.0.4:9866,DS-0a1f5b5a-e07c-415f-ae48-13e5428d67bf,DISK]]


Status: HEALTHY
 Number of data-nodes:  2
 Number of racks:               1
 Total dirs:                    0
 Total symlinks:                0

Replicated Blocks:
 Total size:    71 B
 Total files:   1
 Total blocks (validated):      1 (avg. block size 71 B)
 Minimally replicated blocks:   1 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    1
 Average block replication:     1.0
 Missing blocks:                0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Blocks queued for replication: 0

Erasure Coded Block Groups:
 Total size:    0 B
 Total files:   0
 Total block groups (validated):        0
 Minimally erasure-coded block groups:  0
 Over-erasure-coded block groups:       0
 Under-erasure-coded block groups:      0
 Unsatisfactory placement block groups: 0
 Average block group size:      0.0
 Missing block groups:          0
 Corrupt block groups:          0
 Missing internal blocks:       0
 Blocks queued for replication: 0
FSCK ended at Wed Jan 29 00:36:16 UTC 2025 in 7 milliseconds


The filesystem under path '/example.txt' is HEALTHY
```

---

### ¡Felizidades levantaste el ecosistema Hadoop + Hive con multi-node!