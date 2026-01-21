# Registro de Experimentos - Clúster Big Data

> Documentación detallada de laboratorios y pruebas realizadas

---

## Sesión 1: Setup Inicial [2025-12-03/04]

### Configuración del Entorno

**Objetivo**: Desplegar clúster Big Data con Docker Compose y resolver problemas de integración Git.

**Pasos Realizados**:

1. Clonar repositorio base

git clone https://github.com/mrugankray/Big-Data-Cluster.git

text

2. Resolver dependencia faltante

sudo apt install dbus-x11

text

3. Levantar servicios

sudo docker compose -f basic-hadoop-docker-compose.yaml up -d

text

4. Verificar contenedores activos

sudo docker ps

text

5. Integrar en repositorio del instituto
- Problema detectado: Big-Data-Cluster registrado como submódulo (modo 160000)
- Solución: Eliminar .git interno y re-añadir como carpeta normal

git rm --cached laboratorio-01-cluster-local/Big-Data-Cluster
rm -rf laboratorio-01-cluster-local/Big-Data-Cluster/.git
git add laboratorio-01-cluster-local/Big-Data-Cluster/

text

**Resultado**: ✅ Clúster desplegado y versionado correctamente

**Contenedores Activos**:
- `namenode` - Maestro HDFS, mantiene metadata
- `datanode` - Almacenamiento de bloques
- `resourcemanager` - Gestor de recursos YARN
- `nodemanager` - Worker que ejecuta tareas
- `historyserver` - Logs de jobs MapReduce

**Tiempo Total**: ~45 minutos (incluyendo descarga de imágenes y troubleshooting Git)

**Problemas Resueltos**:

1. **Error `dbus-launch not found`**
- Causa: Falta paquete para gestión de credenciales Docker
- Solución: `sudo apt install dbus-x11`

2. **Warning `version obsolete` en YAML**
- Causa: Archivo usa formato antiguo `version: "3"`
- Solución: Ignorado (cosmético, no afecta funcionalidad)

3. **Submódulo Git no deseado**
- Causa: Clonar repo con .git interno y hacer add sin eliminarlo
- Solución: `git rm --cached` + eliminar .git + re-añadir

**Observaciones Técnicas**:
- Descarga inicial: ~8GB de imágenes Docker
- RAM consumida (clúster activo): ~6GB
- CPU en idle: ~5%
- Tiempo de arranque del clúster: ~2 minutos

**Configuración Aplicada**:

YARN Memory: 16GB
MapReduce Mapper: 4GB
Replication Factor: 3 (por defecto)
Block Size: 128MB (por defecto)

text

---

## Lab 1: Exploración HDFS [Pendiente]

### Objetivo
Familiarizarse con comandos básicos de HDFS y entender la arquitectura de almacenamiento distribuido.

### Comandos Planeados

Crear directorio en HDFS

hdfs dfs -mkdir /user/vincent
Crear archivo de prueba local

echo "Hola desde el clúster Big Data" > test.txt
Subir a HDFS

hdfs dfs -put test.txt /user/vincent/
Listar contenido

hdfs dfs -ls /user/vincent/
Visualizar contenido

hdfs dfs -cat /user/vincent/test.txt
Ver estadísticas del sistema

hdfs dfsadmin -report

text

### Conceptos a Explorar
- Arquitectura namenode/datanode
- Replicación de bloques
- Factor de replicación
- Tolerancia a fallos

### Estado
🔜 Por iniciar

---

## Lab 2: MapReduce WordCount [Pendiente]

### Objetivo
Ejecutar primer job MapReduce para contar palabras en un texto y entender el paradigma de procesamiento distribuido.

### Comandos Planeados

Ejecutar ejemplo WordCount

hadoop jar /opt/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar
wordcount /user/vincent/test.txt /user/vincent/output
Ver resultados

hdfs dfs -cat /user/vincent/output/part-r-00000

text

### Conceptos a Explorar
- Fase Map: división y procesamiento paralelo
- Fase Shuffle: redistribución de datos
- Fase Reduce: agregación de resultados
- YARN resource allocation

### Estado
🔜 Por iniciar

---

## Lab 3: PySpark en Zeppelin [Pendiente]

### Objetivo
Crear notebook interactivo con transformaciones Spark usando RDDs (Resilient Distributed Datasets).

### Código Planeado

%spark.pyspark
Crear RDD simple

data =

​
rdd = sc.parallelize(data)
Transformación: multiplicar por 2

rdd_doble = rdd.map(lambda x: x * 2)
Acción: recolectar resultados

resultado = rdd_doble.collect()
print(resultado)

text

### Conceptos a Explorar
- RDDs vs DataFrames
- Transformaciones lazy (map, filter)
- Acciones (collect, count, reduce)
- DAG (Directed Acyclic Graph) de ejecución

### Estado
🔜 Por iniciar

---

## Lab 4: Pipeline Airflow [Pendiente]

### Objetivo
Crear DAG simple para orquestar tareas de ingesta y procesamiento de datos.

### Estructura Planeada

DAG con 3 tareas:
1. Generar datos
2. Subir a HDFS
3. Ejecutar procesamiento Spark

text

### Conceptos a Explorar
- DAGs (Directed Acyclic Graphs)
- Operators (BashOperator, PythonOperator)
- Dependencias entre tareas
- Scheduling y retries

### Estado
🔜 Por iniciar

---

## Notas Técnicas Generales

### Arquitectura del Clúster

**HDFS (Hadoop Distributed File System)**:
- **Namenode**: Maestro que mantiene el árbol de directorios y metadatos de archivos
- **Datanode**: Workers que almacenan bloques de datos reales
- **Replicación**: Cada bloque se replica 3 veces (configurable) para tolerancia a fallos
- **Block size**: 128MB por defecto, archivos grandes se dividen automáticamente

**YARN (Yet Another Resource Negotiator)**:
- **ResourceManager**: Asigna recursos (CPU, RAM) a las aplicaciones
- **NodeManager**: Gestiona recursos en cada nodo worker
- **ApplicationMaster**: Coordina la ejecución de cada aplicación

**Spark**:
- **Master**: Coordina el clúster Spark
- **Worker**: Ejecuta tareas en paralelo
- **Driver**: Proceso principal de la aplicación
- **Executor**: Procesos en workers que ejecutan tareas

### Accesos Web

- **Namenode UI**: http://localhost:9870/dfshealth.html
  - Ver estado HDFS, explorar archivos, métricas de datanodes

- **Resource Manager**: http://localhost:8088/cluster
  - Ver aplicaciones corriendo, histórico de jobs, métricas YARN

- **Spark Master**: http://localhost:8080
  - Ver workers conectados, aplicaciones activas, recursos asignados

- **Zeppelin**: http://localhost:8082
  - Crear y ejecutar notebooks interactivos

- **Airflow**: http://localhost:3000 (admin/admin)
  - Visualizar DAGs, ejecutar manualmente, ver logs de tareas

### Comandos de Diagnóstico

Ver uso de recursos del clúster

sudo docker stats
Ver logs de todos los contenedores

sudo docker compose -f Big-Data-Cluster/basic-hadoop-docker-compose.yaml logs
Reiniciar contenedor específico sin afectar otros

sudo docker restart namenode
Ver procesos Java corriendo (desde dentro del contenedor)

jps

text

### Buenas Prácticas Aprendidas

1. **Gestión de Repos Git**: Siempre eliminar `.git` de repositorios clonados antes de añadirlos a otro repo
2. **Gestión Docker**: Usar `stop` para pausas cortas, `down` para apagar completamente
3. **Documentación**: Registrar problemas y soluciones en tiempo real
4. **Commits**: Mensajes descriptivos con prefijos `feat:`, `fix:`, `docs:`

---

## Próximos Pasos

1. ✅ Setup del clúster completado
2. ✅ Documentación base creada
3. 🔜 Ejecutar Lab 1: HDFS
4. 🔜 Ejecutar Lab 2: MapReduce
5. 🔜 Ejecutar Lab 3: PySpark
6. 🔜 Ejecutar Lab 4: Airflow
