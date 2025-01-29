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

---

## ¡Listo para crear tablas y hacer consultas!

Una vez completados estos tres pasos, ya puedes crear las tablas y ejecutar consultas en Hive.

### **Importante:**

Al crear las tablas en Hive, **es necesario** utilizar la siguiente ubicación para los datos en HDFS (y **no** la que indican las instrucciones originales de `Big-Data-Workshops-Hadoop-Hive`):

```sql
LOCATION 'hdfs://namenode:9000/datasets';
```

Con esto, evitarás problemas de acceso y asegurarte de que las tablas se creen correctamente en la ubicación adecuada.

---

### 🚀 ¡Ya puedes empezar a trabajar con Hadoop y Hive!

```
 docker exec -it namenode hdfs fsck /example.txt -files -blocks -locations
```
