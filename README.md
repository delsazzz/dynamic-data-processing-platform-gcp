# Plataforma de Procesamiento Dinámico de Datos en Google Cloud Platform

## Descripción

Este proyecto consiste en una plataforma de procesamiento de datos desarrollada sobre Google Cloud Platform (GCP) durante mis prácticas del Grado Superior en Desarrollo de Aplicaciones Multiplataforma (DAM) en la empresa Road to Data (r2d).

La solución fue diseñada para automatizar la carga, validación, transformación y almacenamiento de archivos Excel (.xlsx) y CSV (.csv) almacenados en Google Cloud Storage, permitiendo su procesamiento dinámico y su posterior carga en BigQuery mediante una arquitectura escalable basada en servicios cloud.

Además del procesamiento de datos, la plataforma incorpora sistemas de auditoría, control de concurrencia, gestión de errores, notificaciones automáticas y despliegue continuo.

> **Nota:** Por motivos de confidencialidad y propiedad intelectual, el código fuente original del proyecto no se encuentra disponible públicamente. Este repositorio documenta la arquitectura, funcionalidades y tecnologías utilizadas.

---

# Objetivos del proyecto

La plataforma fue desarrollada con los siguientes objetivos:

* Automatizar la carga de archivos Excel y CSV.
* Permitir configuraciones dinámicas sin modificar código.
* Procesar estructuras de datos heterogéneas.
* Transformar archivos pivotados cuando sea necesario.
* Cargar automáticamente la información en BigQuery.
* Mantener trazabilidad completa de todas las ejecuciones.
* Evitar reprocesamientos y duplicidades.
* Generar alertas automáticas ante errores.
* Gestionar el histórico de archivos procesados.

---

# Arquitectura de la solución

La solución se basa en una arquitectura orientada a eventos utilizando servicios gestionados de Google Cloud Platform.

## Componentes principales

| Servicio             | Función                                       |
| -------------------- | --------------------------------------------- |
| Google Cloud Storage | Almacenamiento de archivos origen             |
| BigQuery             | Configuración, almacenamiento de datos y logs |
| Cloud Run Job        | Ejecución del procesamiento                   |
| Cloud Run Service    | Gestión de alertas                            |
| Pub/Sub              | Comunicación entre componentes                |
| Cloud Scheduler      | Ejecuciones programadas                       |
| Cloud Build          | Automatización de despliegues                 |
| Artifact Registry    | Almacenamiento de imágenes Docker             |
| GitHub               | Control de versiones                          |

## Flujo general

```text
GitHub
   │
   ▼
Cloud Build
   │
   ▼
Artifact Registry
   │
   ▼
Cloud Run Job
   │
   ▼
Google Cloud Storage
   │
   ▼
Procesamiento de datos
   │
   ▼
BigQuery

Errores
   │
   ▼
Pub/Sub
   │
   ▼
Cloud Run Service
   │
   ▼
Notificación por correo electrónico
```

---

# Funcionalidades principales

## Configuración dinámica

El comportamiento del sistema se controla mediante una tabla de configuración almacenada en BigQuery.

Esto permite:

* Añadir nuevos tipos de archivos sin modificar el código.
* Configurar tablas destino.
* Definir filas a ignorar.
* Configurar transformaciones específicas.
* Gestionar destinatarios de alertas.

## Detección automática de archivos

El sistema detecta automáticamente los archivos almacenados en Google Cloud Storage y busca la configuración correspondiente para procesarlos.

Formatos soportados:

* Excel (.xlsx)
* CSV (.csv)

## Procesamiento y transformación de datos

La plataforma permite:

* Detección automática de cabeceras.
* Normalización de columnas.
* Procesamiento de múltiples hojas.
* Transformaciones pivot y unpivot.
* Consolidación de información procedente de varios archivos.
* Adaptación dinámica según la configuración definida.

## Carga en BigQuery

Una vez transformados, los datos se cargan automáticamente en BigQuery.

Se soportan diferentes estrategias de carga:

* WRITE_TRUNCATE
* Tablas consolidadas
* Agregación de múltiples archivos

## Sistema de logs

Todas las ejecuciones quedan registradas en una tabla de auditoría.

Información registrada:

* Identificador de ejecución.
* Archivo procesado.
* Configuración utilizada.
* Fecha y hora de ejecución.
* Duración del proceso.
* Número de filas procesadas.
* Estado final.
* Mensajes de error.
* Huella de error.
* Estado de notificación.

Estados posibles:

* RUNNING
* SUCCESS
* ERROR
* NO_CONFIG

## Sistema de alertas

Ante cualquier error durante el procesamiento:

1. Se genera una alerta.
2. Se publica un mensaje en Pub/Sub.
3. Un Cloud Run Service consume la notificación.
4. Se envía automáticamente un correo electrónico a los destinatarios configurados.

---

# Control de versiones de archivos

Para evitar procesamientos duplicados, el sistema utiliza el identificador único **Generation** proporcionado por Google Cloud Storage.

Esto permite:

* Procesar una única vez cada versión de un archivo.
* Detectar nuevas versiones de un mismo fichero.
* Evitar cargas duplicadas.
* Mantener la integridad de los datos.

Ejemplo:

| Archivo     | Generation | Estado  |
| ----------- | ---------- | ------- |
| ventas.xlsx | 123456789  | ERROR   |
| ventas.xlsx | 987654321  | SUCCESS |

---

# Control de concurrencia

Para evitar que dos ejecuciones intenten procesar simultáneamente el mismo archivo, se implementó un sistema de bloqueo mediante estados en la tabla de logs.

Cuando un archivo comienza a procesarse:

* Se registra con estado RUNNING.
* El resto de ejecuciones lo ignoran temporalmente.
* Una vez finalizado, el estado pasa a SUCCESS o ERROR.

Esto garantiza consistencia y evita duplicidades.

---

# Automatización y despliegue

El despliegue de la solución se automatizó mediante integración continua utilizando GitHub y Cloud Build.

Proceso de despliegue:

1. Push al repositorio.
2. Activación automática de Cloud Build.
3. Construcción de imagen Docker.
4. Publicación en Artifact Registry.
5. Despliegue automático en Cloud Run Job.

Además, se configuró Cloud Scheduler para permitir ejecuciones programadas mediante expresiones CRON.

---

# Retos técnicos abordados

Durante el desarrollo del proyecto se resolvieron distintos desafíos técnicos:

* Procesamiento de estructuras Excel heterogéneas.
* Configuración dinámica sin cambios de código.
* Prevención de cargas duplicadas.
* Gestión de múltiples versiones de archivos.
* Control de concurrencia.
* Sistema de auditoría completo.
* Gestión centralizada de errores.
* Notificaciones automáticas desacopladas.
* Automatización del despliegue mediante CI/CD.

---

# Tecnologías utilizadas

* Python
* Pandas
* OpenPyXL
* SQL
* Google Cloud Platform
* BigQuery
* Google Cloud Storage
* Cloud Run Jobs
* Cloud Run Services
* Pub/Sub
* Cloud Scheduler
* Cloud Build
* Artifact Registry
* Docker
* GitHub

---

# Competencias demostradas

Este proyecto me permitió adquirir experiencia práctica en:

* Data Engineering
* Desarrollo ETL
* Arquitecturas orientadas a eventos
* Computación en la nube
* Automatización de procesos
* Diseño de pipelines de datos
* CI/CD
* Monitorización y observabilidad
* Gestión de errores y alertas
* Procesamiento de datos con Python

---

# Contexto

Proyecto desarrollado durante mis prácticas del Grado Superior en Desarrollo de Aplicaciones Multiplataforma (DAM), participando en el diseño e implementación de una solución cloud para la automatización de procesos de carga y transformación de datos sobre Google Cloud Platform.
