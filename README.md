# Data Platform Team Lead - Technical Challenge

## Scenario: Federated Data Ingestion & Processing on GCP
You've recently joined a global airline group operating in multiple regions. Each region has developed its own local data infrastructure, storing data in regional GCP projects across various formats and systems, including BigQuery, Cloud Storage, and some PostgreSQL on CloudSQL instances.

The leadership team is pushing to enable cross-regional analytics for core business metrics like route profitability, fuel efficiency, and customer satisfaction. However, data silos, inconsistent schemas, and latency issues have made it difficult to build a unified, scalable analytics platform.

## Requirements

You are tasked with designing and leading the implementation of a federated data ingestion and processing framework that supports:

Real-time and batch ingestion from heterogeneous sources (BigQuery, GCS, PostgreSQL on CloudSQL).

Federated querying capabilities across multiple GCP projects.

Schema harmonization and data governance enforcement.

Efficient data processing pipelines for both exploratory and scheduled analytics

## Deliverable

A diagram and a brief description (1–2 pages) GCP services used (e.g., Dataflow, BigQuery Omni, Pub/Sub, Cloud Functions, Dataplex, etc.)

How data will be ingested, cataloged, and processed across regions
How you'd manage schema consistency and versioning
How you would approach cross-region query performance and cost optimization
Any trade-offs and fallback strategies (e.g., caching, materialized views)

## Instructions
1. Your solution must be in a public GitHub repository.
2. To submit your challenge, you must make a `POST` request to `https://advana-challenge-check-api-cr-k4hdbggvoq-uc.a.run.app/data-engineer`. This is an example of the body you must send:
```json
    {
      "name": "Juan Perez",
      "mail": "juan.perez@example.com",
      "github_url": "https://github.com/juanperez/latam-challenge.git"
    }
```

PLEASE, SEND THE REQUEST ONLY ONCE.
If your request was successful, you will receive this message:

```
jsonCopiar{
  "status": "OK",
  "detail": "your request was received"
}
```

## Solution (In Spanish from here!)

---

## Descripción y Justificación de la Arquitectura

### 1. Ingesta Federada y Origen de Datos
Los datos provienen de distintas ubicaciones:

- **BigQuery en múltiples proyectos o regiones**  
- **Cloud Storage** con archivos planos (CSV, Parquet, JSON, etc.)  
- **CloudSQL (PostgreSQL)** para datos transaccionales.  

Para la _ingesta en tiempo real_, se utilizan **Pub/Sub** y, en la parte de _ingesta batch_, se aprovechan **conectores nativos** de BigQuery o **Dataflow** para extraer datos de Cloud Storage y CloudSQL.

### 2. Transformación y Procesamiento (Dataflow)
**Dataflow** sirve como la pieza central para el procesamiento de datos:
- **Streaming**: Captura eventos en tiempo real desde Pub/Sub y los inserta o transforma camino a BigQuery.  
- **Batch**: Ejecuta cargas programadas y transforma datos de archivos en Cloud Storage o lecturas desde CloudSQL.

De este modo, la lógica de transformación se centraliza, permitiendo escalar y orquestar de forma confiable.

### 3. Catalogación y Gobernanza (Dataplex, Data Catalog)
Para mantener consistencia y coherencia:
- **Dataplex** unifica la seguridad, el linaje de datos y las políticas de acceso a lo largo de múltiples lagos de datos y regiones.  
- **Data Catalog** ofrece un índice unificado de metadatos, facilitando la búsqueda, etiquetado y versionado de esquemas.

De esta forma, se garantiza que todos los equipos conozcan la estructura de los datos y que se cumplan las políticas de gobernanza.

### 4. BigQuery Centralizado u Omni
El núcleo de la analítica se apoya en **BigQuery**:

- **Federated queries** o **BigQuery Omni** permiten acceder a datos en otras regiones o incluso en otras nubes sin necesidad de migrarlos completamente.  
- **Tablas materializadas** o **copias parciales** pueden usarse para reducir costos de egress entre regiones y mejorar el rendimiento en consultas muy frecuentes.  
- **Particionado y clustering** ayudan a optimizar el desempeño y bajar costos de escaneo de datos.

### 5. Estrategias de Optimización y Fallback
- Emplear **vistas materializadas** o **caching** en BigQuery para acelerar consultas repetitivas.  
- Crear particiones por fecha o por regiones para minimizar el volumen de datos escaneados.  
- Si se detecta latencia o costos excesivos, conviene replicar subconjuntos clave de datos en la región más consultada.

### 6. Herramientas de Analítica
Para la capa de consumo:
- Herramientas como **Looker** o **Data Studio** (entre otras de BI) se conectan directamente a BigQuery para generar reportes, dashboards y análisis ad-hoc.  
- La federación de datos queda transparente para los usuarios finales, ya que BigQuery se encarga de la orquestación subyacente.

---

## Conclusión

Esta arquitectura resuelve la dispersión y la heterogeneidad de datos mediante **ingesta federada**, normalización y **gobernanza centralizada**. Al combinar servicios como **Dataflow** (para procesamiento en streaming y batch), **BigQuery** (para analítica y federación) y **Dataplex**/**Data Catalog** (para la gestión y descubrimiento de datos), se garantiza un entorno escalable, confiable y optimizado para análisis cross-region. Además, la capa de BI se facilita al exponer un punto único de acceso a la información, independiente de dónde resida físicamente cada fuente.

