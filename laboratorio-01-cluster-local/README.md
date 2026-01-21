# Laboratorio 1: Clúster Big Data Local con Docker

**Asignatura**: Big Data Aplicado  
**Curso**: 2025-2026  
**Centro**: IES Rafael Alberti

---

## Descripción

Despliegue de ecosistema Big Data completo usando Docker Compose para experimentación con arquitecturas distribuidas Hadoop/Spark.

## Componentes Desplegados

| Tecnología | Versión | Puerto | Función |
|------------|---------|--------|---------|
| Hadoop HDFS | 3.2.1 | 9870 | Sistema archivos distribuido |
| YARN | 3.2.1 | 8088 | Gestor de recursos |
| Spark | 3.2.2 | 8080 | Procesamiento en memoria |
| Zeppelin | 0.10.1 | 8082 | Notebooks interactivos |
| Airflow | 2.3.3 | 3000 | Orquestación ETL |
| Flume | 1.11.0 | - | Ingesta streaming |

---

## Comandos Esenciales

### Gestión del Clúster

**Levantar (desde Big-Data-Cluster/)**

```sudo docker compose -f basic-hadoop-docker-compose.yaml up -d```

**Ver estado de contenedores**

```sudo docker ps```

**Detener (conserva datos)**

```sudo docker compose -f basic-hadoop-docker-compose.yaml stop```

**Reiniciar**

```sudo docker compose -f basic-hadoop-docker-compose.yaml start```

**Ver logs de contenedor**

```sudo docker logs -f <nombre-contenedor>```

**Apagar y eliminar contenedores (conserva volúmenes)**

```sudo docker compose -f basic-hadoop-docker-compose.yaml down```

### Acceso a Contenedores

**Entrar al namenode (nodo maestro HDFS)**

```sudo docker exec -it namenode /bin/bash```

**Comandos HDFS básicos (dentro del contenedor)**

```bash
hdfs dfs -ls / # Listar raíz HDFS
hdfs dfs -mkdir /user/vincent # Crear directorio
hdfs dfs -put archivo.txt /user/vincent # Subir archivo
hdfs dfs -cat /user/vincent/archivo.txt # Leer archivo
hdfs dfs -get /user/vincent/archivo.txt # Descargar
```
---

## Interfaces Web

| Servicio | URL | Usuario | Contraseña |
|----------|-----|---------|------------|
| **Namenode UI** | http://localhost:9870 | - | - |
| **Resource Manager** | http://localhost:8088 | - | - |
| **History Server** | http://localhost:8188 | - | - |
| **Spark Master** | http://localhost:8080 | - | - |
| **Zeppelin** | http://localhost:8082 | - | - |
| **Airflow** | http://localhost:3000 | admin | admin |

---

## Laboratorios

Ver documentación detallada en [`docs/EXPERIMENTOS.md`](docs/EXPERIMENTOS.md)

- [ ] **Lab 1**: Exploración HDFS - comandos básicos
- [ ] **Lab 2**: Job MapReduce WordCount
- [ ] **Lab 3**: Notebook PySpark en Zeppelin
- [ ] **Lab 4**: Pipeline básico con Airflow

---

## Configuración Hardware

- **Portátil**: ASUS TUF Gaming A15
- **SO**: Pop!OS 22.04 LTS
- **RAM**: 16GB asignados al clúster
- **CPU**: 8 cores (hyper-threading)
- **Docker**: 28.2.2

### Configuración YARN

Ver en `Big-Data-Cluster/hadoop.env`

```bash
YARN_CONF_yarn_nodemanager_resource_memory___mb=16384
YARN_CONF_yarn_scheduler_capacity_root_default_maximum___allocation___mb=8192
MAPRED_CONF_mapreduce_map_memory_mb=4096
```

---

## Resolución de Problemas

### Error: `dbus-launch not found`
**Solución**: `sudo apt install dbus-x11`

### Contenedor no arranca

#### Ver logs

```sudo docker logs <nombre-contenedor>```

#### Reiniciar contenedor específico

```sudo docker restart <nombre-contenedor>```

### Sin espacio en disco

#### Ver uso

```df -h```

#### Limpiar imágenes y contenedores no usados

```sudo docker system prune -a```

---

## Recursos

- **Repositorio base**: [mrugankray/Big-Data-Cluster](https://github.com/mrugankray/Big-Data-Cluster)
- **Material curso**: [aitor-medrano.github.io/iabd](https://aitor-medrano.github.io/iabd)
- **Documentación oficial**:
  - [Apache Hadoop](https://hadoop.apache.org/docs/r3.2.1/)
  - [Apache Spark](https://spark.apache.org/docs/3.2.2/)
  - [Apache Airflow](https://airflow.apache.org/docs/apache-airflow/2.3.3/)

---

## Estructura del Proyecto

```
laboratorio-01-cluster-local/
├── Big-Data-Cluster/ # Configuración Docker Compose
│ ├── basic-hadoop-docker-compose.yaml
│ ├── hadoop.env
│ ├── configs/
│ └── ...
├── docs/
│ ├── EXPERIMENTOS.md # Registro detallado de labs
│ └── screenshots/ # Capturas de UIs
├── notebooks/ # Notebooks exportados de Zeppelin
├── scripts/ # Scripts auxiliares
├── .gitignore # Exclusiones Git
└── README.md # Este archivo```