---
title: Métricas Basadas en Logs
description: "Google Cloud log-based metrics: métricas de conteo y distribución sobre entradas de log, alertas sobre métricas de logs, caso de uso con GKE."
date: 2026-04-09
type: topic
subject: Google Cloud
sources: [GSP091 — Creating and Alerting on Logs-based Metrics]
tags: [google-cloud, gcp, logging, log-based-metrics, alertas, gke, observabilidad, personal, lab]
---

# Métricas Basadas en Logs

## ¿Qué son?

Las **métricas basadas en logs** (log-based metrics) permiten crear métricas de Cloud Monitoring a partir de **entradas de log** en Cloud Logging. En lugar de instrumentar el código para emitir una métrica, se define un **filtro sobre los logs** y Cloud Monitoring genera automáticamente la serie temporal.

<svg viewBox="0 0 500 110" width="500" height="110" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <!-- Logs input -->
  <rect x="10" y="25" width="110" height="60" rx="6" fill="#4a7fa5" opacity="0.25"/>
  <text x="65" y="48" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Cloud Logging</text>
  <text x="65" y="61" font-size="8" fill="currentColor" text-anchor="middle">Entradas de log</text>
  <text x="65" y="72" font-size="8" fill="currentColor" text-anchor="middle">de cualquier servicio</text>
  <!-- Arrow -->
  <line x1="120" y1="55" x2="155" y2="55" stroke="currentColor" stroke-width="1.5" marker-end="url(#lbm1)"/>
  <!-- Filter -->
  <rect x="158" y="25" width="110" height="60" rx="6" fill="#c0582a" opacity="0.25"/>
  <text x="213" y="48" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Filtro de logs</text>
  <text x="213" y="61" font-size="8" fill="currentColor" text-anchor="middle">severity=ERROR</text>
  <text x="213" y="72" font-size="8" fill="currentColor" text-anchor="middle">textPayload~"timeout"</text>
  <!-- Arrow -->
  <line x1="268" y1="55" x2="303" y2="55" stroke="currentColor" stroke-width="1.5" marker-end="url(#lbm1)"/>
  <!-- Metric -->
  <rect x="306" y="25" width="100" height="60" rx="6" fill="#5ba85e" opacity="0.25"/>
  <text x="356" y="45" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Métrica</text>
  <text x="356" y="58" font-size="8" fill="currentColor" text-anchor="middle">Serie temporal</text>
  <text x="356" y="69" font-size="8" fill="currentColor" text-anchor="middle">en Monitoring</text>
  <!-- Arrow -->
  <line x1="406" y1="55" x2="441" y2="55" stroke="currentColor" stroke-width="1.5" marker-end="url(#lbm1)"/>
  <!-- Alert -->
  <rect x="444" y="30" width="48" height="50" rx="5" fill="#888" opacity="0.25"/>
  <text x="468" y="50" font-size="8" fill="currentColor" text-anchor="middle">Alerta</text>
  <text x="468" y="62" font-size="8" fill="currentColor" text-anchor="middle">/ Dashboard</text>
  <defs>
    <marker id="lbm1" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
  </defs>
</svg>

---

## Tipos de métricas basadas en logs

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| **Contador** (Counter) | Cuenta cuántas veces aparece una entrada que cumple el filtro | Número de errores 500 por minuto |
| **Distribución** (Distribution) | Extrae un valor numérico de los logs y calcula percentiles | Latencia de respuesta p50/p95/p99 |
| **Booleana** (Boolean) | Indica si en el periodo hubo al menos una entrada | ¿Hubo algún error crítico? |

---

## Lab GSP091 — Métricas de logs con GKE

El lab usa un clúster de **Google Kubernetes Engine (GKE)** para generar tráfico y errores, y luego crear métricas y alertas sobre esos logs.

### Contexto del lab

```
Clúster GKE → genera logs → Cloud Logging → filtro → métrica → alerta → notificación
```

### 1. Crear una métrica de logs definida por el usuario

Desde la consola:
1. **Logging** → **Logs-based metrics** → **Create metric**.
2. Configurar:
   - **Metric type**: Counter (conteo) o Distribution (distribución).
   - **Name**: nombre descriptivo (ej. `errores_app`).
   - **Filter**: expresión de filtro de logs (ver ejemplos abajo).
   - Para Distribution: seleccionar el **campo** del log del que extraer el valor numérico.
3. Crear la métrica.

Desde CLI:

```bash
# Crear métrica de conteo (contador de errores)
gcloud logging metrics create errores_app \
  --description="Cuenta entradas de log con severity=ERROR" \
  --log-filter='severity=ERROR resource.type="k8s_container"'

# Crear métrica de distribución (latencia)
gcloud logging metrics create latencia_app \
  --description="Distribución de latencia en ms" \
  --log-filter='resource.type="k8s_container" jsonPayload.latency>0' \
  --value-extractor='EXTRACT(jsonPayload.latency)'
```

### 2. Sintaxis de filtros de logs

```
# Filtrar por severidad
severity=ERROR
severity>=WARNING

# Filtrar por tipo de recurso
resource.type="gce_instance"
resource.type="k8s_container"
resource.type="cloud_function"

# Filtrar por contenido del log
textPayload:"timeout"
jsonPayload.message:"connection refused"

# Combinar condiciones
severity=ERROR AND resource.type="k8s_container" AND resource.labels.cluster_name="mi-cluster"

# Filtrar por nombre de log
logName="projects/PROYECTO/logs/run.googleapis.com%2Frequests"

# Excluir entradas
NOT textPayload:"health check"
```

### 3. Crear una alerta basada en la métrica de logs

Una vez creada la métrica, se puede usar como cualquier otra métrica de Cloud Monitoring.

Desde la consola:
1. **Monitoring** → **Alerting** → **Create policy**.
2. En **Metric**, buscar `logging/user/NOMBRE_METRICA` (las métricas de usuario van bajo el prefijo `logging/user/`).
3. Configurar la condición (ej: valor > 5 durante 1 minuto).
4. Añadir canal de notificación.
5. Guardar.

Desde CLI:

```bash
# Crear política de alertas sobre la métrica de logs
gcloud alpha monitoring policies create \
  --display-name="Alerta errores app" \
  --condition-display-name="Errores > 5/min" \
  --condition-filter='metric.type="logging.googleapis.com/user/errores_app"' \
  --condition-threshold-value=5 \
  --condition-threshold-duration=60s \
  --notification-channels=CANAL_ID
```

---

## Métricas de logs del sistema (predefinidas por Google)

Google también crea automáticamente algunas métricas de logs predefinidas:

| Métrica | Descripción |
|---------|-------------|
| `logging.googleapis.com/log_entry_count` | Total de entradas de log por proyecto |
| `logging.googleapis.com/exports/byte_count` | Bytes exportados a sinks |
| `logging.googleapis.com/exclusion/dropped_log_entry_count` | Entradas excluidas |

Estas métricas están disponibles automáticamente sin configuración.

---

## Alertas de logs directas (Log-based Alerts)

Además de métricas, Cloud Logging permite crear **alertas directas sobre logs** sin pasar por Cloud Monitoring:

1. **Logging** → **Log-based alerts** → **Create alert**.
2. Definir el filtro.
3. Configurar la frecuencia de notificación (para evitar spam si el evento es frecuente).
4. Seleccionar canales de notificación.

**Diferencia clave:**
- **Log-based alert**: disparo inmediato cuando aparece una entrada de log que cumple el filtro.
- **Metric-based alert**: disparo cuando una métrica (derivada de logs) supera un umbral sostenido en el tiempo.

---

## Exportar logs (Log Sinks)

Los logs se pueden exportar a Cloud Storage, BigQuery o Pub/Sub para retención a largo plazo o análisis:

```bash
# Exportar logs de error a Cloud Storage
gcloud logging sinks create sink-errores \
  storage.googleapis.com/NOMBRE_BUCKET \
  --log-filter='severity>=ERROR'

# Exportar todos los logs a BigQuery
gcloud logging sinks create sink-bigquery \
  bigquery.googleapis.com/projects/PROYECTO/datasets/DATASET \
  --log-filter=''

# Listar sinks existentes
gcloud logging sinks list

# Ver un sink
gcloud logging sinks describe sink-errores
```

---

## Caso de uso completo — GKE con alertas automáticas

```
1. Clúster GKE genera logs de errores en jsonPayload.level="error"
2. Filtro: resource.type="k8s_container" AND jsonPayload.level="error"
3. Métrica de conteo: errores_gke (se incrementa por cada log filtrado)
4. Dashboard: gráfico de líneas mostrando errores/minuto
5. Alerta: si errores_gke > 10/min durante 2 min → enviar Slack
6. Acción automática: Pub/Sub → Cloud Function → restart del pod en error
```

---

## Related Topics

- [[google-cloud/monitoreo/Cloud-Monitoring]] — dashboards, uptime checks y alertas métricas
- [[google-cloud/funciones/Cloud-Run-Functions]] — automatizar respuestas a alertas con funciones
- [[google-cloud/iam/IAM-Roles-Personalizados]] — permisos para crear métricas y alertas
