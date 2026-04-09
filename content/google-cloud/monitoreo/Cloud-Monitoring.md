---
title: Cloud Monitoring
description: "Google Cloud Monitoring: dashboards, uptime checks, alertas, grupos de monitoreo, métricas multi-proyecto, Cloud Logging."
date: 2026-04-09
type: topic
subject: Google Cloud
sources: [GSP089 — Cloud Monitoring Qwik Start, GSP090 — Monitoring Multiple Projects]
tags: [google-cloud, gcp, monitoring, alertas, dashboards, uptime, observabilidad, personal, lab]
---

# Cloud Monitoring

## ¿Qué es Cloud Monitoring?

**Cloud Monitoring** (parte de Google Cloud Observability) proporciona visibilidad sobre el rendimiento, disponibilidad y salud de las aplicaciones e infraestructura en Google Cloud, en otras nubes y on-premise.

<svg viewBox="0 0 500 140" width="500" height="140" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <rect x="10" y="15" width="110" height="110" rx="6" fill="#4a7fa5" opacity="0.15"/>
  <text x="65" y="33" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">Fuentes de datos</text>
  <rect x="20" y="40" width="90" height="18" rx="3" fill="#4a7fa5" opacity="0.35"/>
  <text x="65" y="53" font-size="8" fill="currentColor" text-anchor="middle">VMs / GKE</text>
  <rect x="20" y="63" width="90" height="18" rx="3" fill="#4a7fa5" opacity="0.35"/>
  <text x="65" y="76" font-size="8" fill="currentColor" text-anchor="middle">Cloud Run / Functions</text>
  <rect x="20" y="86" width="90" height="18" rx="3" fill="#4a7fa5" opacity="0.35"/>
  <text x="65" y="99" font-size="8" fill="currentColor" text-anchor="middle">Servicios gestionados</text>
  <rect x="20" y="109" width="90" height="10" rx="3" fill="#4a7fa5" opacity="0.35"/>
  <text x="65" y="118" font-size="7" fill="currentColor" text-anchor="middle">Apps on-premise / AWS</text>
  <line x1="120" y1="70" x2="155" y2="70" stroke="currentColor" stroke-width="1.5" marker-end="url(#cm1)"/>
  <rect x="158" y="20" width="150" height="100" rx="6" fill="#c0582a" opacity="0.2"/>
  <text x="233" y="42" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Cloud Monitoring</text>
  <text x="233" y="57" font-size="8" fill="currentColor" text-anchor="middle">Métricas + Series temporales</text>
  <text x="233" y="70" font-size="8" fill="currentColor" text-anchor="middle">Dashboards</text>
  <text x="233" y="83" font-size="8" fill="currentColor" text-anchor="middle">Uptime Checks</text>
  <text x="233" y="96" font-size="8" fill="currentColor" text-anchor="middle">Políticas de alertas</text>
  <text x="233" y="109" font-size="8" fill="currentColor" text-anchor="middle">Grupos de monitoreo</text>
  <line x1="308" y1="70" x2="343" y2="70" stroke="currentColor" stroke-width="1.5" marker-end="url(#cm1)"/>
  <rect x="346" y="20" width="140" height="100" rx="6" fill="#5ba85e" opacity="0.15"/>
  <text x="416" y="42" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">Notificaciones</text>
  <rect x="356" y="50" width="120" height="16" rx="3" fill="#5ba85e" opacity="0.3"/>
  <text x="416" y="62" font-size="8" fill="currentColor" text-anchor="middle">Email / SMS</text>
  <rect x="356" y="71" width="120" height="16" rx="3" fill="#5ba85e" opacity="0.3"/>
  <text x="416" y="83" font-size="8" fill="currentColor" text-anchor="middle">PagerDuty / Slack</text>
  <rect x="356" y="92" width="120" height="16" rx="3" fill="#5ba85e" opacity="0.3"/>
  <text x="416" y="104" font-size="8" fill="currentColor" text-anchor="middle">Pub/Sub / Webhooks</text>
  <defs>
    <marker id="cm1" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
  </defs>
</svg>

---

## Lab GSP089 — Cloud Monitoring básico con VMs

### 1. Instalar el agente de Monitoring en una VM

```bash
# Instalar el agente Ops (recomendado — reemplaza al agente legacy)
curl -sSO https://dl.google.com/cloudagents/add-google-cloud-ops-agent-repo.sh
sudo bash add-google-cloud-ops-agent-repo.sh --also-install

# Verificar que el agente está activo
sudo systemctl status google-cloud-ops-agent"*"
```

El agente recopila métricas del sistema (CPU, memoria, disco, red) y logs, y los envía a Cloud Monitoring.

### 2. Crear un Uptime Check (verificación de disponibilidad)

Un **Uptime Check** verifica periódicamente que un recurso responde correctamente.

Desde la consola:
1. **Monitoring** → **Uptime checks** → **Create uptime check**.
2. Configurar:
   - **Protocol**: HTTP / HTTPS / TCP.
   - **Resource type**: URL, instancia de VM, balanceador de carga…
   - **Frequency**: cada 1 min (mínimo), 5 min, 10 min o 15 min.
   - **Regions**: seleccionar desde qué regiones se lanza el check.
3. Opcionalmente configurar **Response validation** (buscar un texto en la respuesta).
4. Crear una alerta asociada al check.

### 3. Crear una política de alertas

Una **política de alertas** define cuándo enviar notificaciones.

Desde la consola:
1. **Monitoring** → **Alerting** → **Create policy**.
2. Seleccionar **métrica** y configurar la condición (umbral, duración).
3. Configurar **canales de notificación** (email, Slack, PagerDuty…).
4. Dar nombre y guardar.

```
Ejemplo: CPU de una VM > 80% durante más de 5 minutos → enviar email
```

### 4. Crear un Dashboard personalizado

1. **Monitoring** → **Dashboards** → **Create dashboard**.
2. Añadir widgets: gráficos de líneas, indicadores, mapas de calor, tablas.
3. Cada widget se configura con una **métrica** y **filtros** (proyecto, recurso, etiqueta).

---

## Lab GSP090 — Monitoreo multi-proyecto

### Ámbito de métricas (Metrics Scope)

Por defecto, un proyecto solo ve sus propias métricas. Con **Metrics Scope** se pueden agregar métricas de múltiples proyectos en un único panel de monitoreo.

<svg viewBox="0 0 500 120" width="500" height="120" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <rect x="160" y="10" width="180" height="50" rx="6" fill="#4a7fa5" opacity="0.35"/>
  <text x="250" y="30" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Proyecto de monitoreo</text>
  <text x="250" y="45" font-size="8" fill="currentColor" text-anchor="middle">(scoping project)</text>
  <rect x="10" y="80" width="130" height="35" rx="5" fill="#5ba85e" opacity="0.3"/>
  <text x="75" y="102" font-size="9" fill="currentColor" text-anchor="middle">Proyecto A</text>
  <rect x="185" y="80" width="130" height="35" rx="5" fill="#5ba85e" opacity="0.3"/>
  <text x="250" y="102" font-size="9" fill="currentColor" text-anchor="middle">Proyecto B</text>
  <rect x="360" y="80" width="130" height="35" rx="5" fill="#5ba85e" opacity="0.3"/>
  <text x="425" y="102" font-size="9" fill="currentColor" text-anchor="middle">Proyecto C</text>
  <line x1="75" y1="80" x2="200" y2="60" stroke="currentColor" stroke-width="1.5" marker-end="url(#mp1)"/>
  <line x1="250" y1="80" x2="250" y2="60" stroke="currentColor" stroke-width="1.5" marker-end="url(#mp1)"/>
  <line x1="425" y1="80" x2="300" y2="60" stroke="currentColor" stroke-width="1.5" marker-end="url(#mp1)"/>
  <defs>
    <marker id="mp1" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
  </defs>
</svg>

```
Proyecto de monitoreo (scoping project) → agrega métricas de → Proyectos monitorizados
```

**Configurar el Metrics Scope:**
1. En el proyecto de monitoreo: **Monitoring** → **Settings** → **Metric scope**.
2. Pulsar **Add projects**.
3. Seleccionar los proyectos a incluir.

### Grupos de monitoreo

Los **grupos** permiten organizar recursos de múltiples proyectos por criterios dinámicos (etiquetas, tipo de recurso, nombre…).

1. **Monitoring** → **Groups** → **Create group**.
2. Definir criterios de filtro (ej: `name contains "web-server"`).
3. El grupo se actualiza automáticamente cuando hay nuevos recursos que cumplen el criterio.

**Ventaja**: las políticas de alertas se pueden aplicar al grupo completo, no a cada recurso individualmente.

### Subgrupos

Un grupo puede tener **subgrupos** para una organización más granular (ej: grupo "Producción" con subgrupos "Web", "Base de datos", "Cache").

---

## Métricas de sistema más usadas

| Métrica | Descripción |
|---------|-------------|
| `compute.googleapis.com/instance/cpu/utilization` | % de CPU de una VM |
| `compute.googleapis.com/instance/disk/read_ops_count` | Operaciones de lectura de disco |
| `compute.googleapis.com/instance/network/received_bytes_count` | Bytes recibidos por la red |
| `run.googleapis.com/request_count` | Peticiones a Cloud Run |
| `run.googleapis.com/request_latencies` | Latencia de Cloud Run |
| `cloudfunctions.googleapis.com/function/execution_count` | Ejecuciones de Cloud Functions |
| `storage.googleapis.com/api/request_count` | Peticiones a Cloud Storage |

---

## Cloud Logging

**Cloud Logging** se integra con Cloud Monitoring y almacena todos los logs de los servicios de GCP.

```bash
# Ver logs de una VM desde CLI
gcloud logging read "resource.type=gce_instance AND resource.labels.instance_id=ID_INSTANCIA" \
  --limit=50

# Ver logs de Cloud Run Functions
gcloud logging read "resource.type=cloud_function AND resource.labels.function_name=NOMBRE" \
  --limit=50 \
  --format=json

# Exportar logs a Cloud Storage
gcloud logging sinks create mi-sink-storage \
  storage.googleapis.com/NOMBRE_BUCKET \
  --log-filter="severity>=ERROR"
```

---

## Canales de notificación

| Canal | Cuándo usarlo |
|-------|--------------|
| **Email** | Alertas no urgentes, informes diarios |
| **SMS** | Alertas críticas que requieren respuesta rápida |
| **PagerDuty** | Sistemas on-call con rotación de guardia |
| **Slack** | Alertas de equipo en tiempo real |
| **Pub/Sub** | Automatización (disparar Cloud Functions al alertar) |
| **Webhook** | Integraciones personalizadas |

---

## Related Topics

- [[google-cloud/monitoreo/Metricas-Basadas-en-Logs]] — métricas derivadas de entradas de log
- [[google-cloud/funciones/Cloud-Run-Functions]] — monitorizar ejecuciones de funciones
- [[google-cloud/iam/IAM-Roles-Personalizados]] — permisos necesarios para configurar alertas
- [[google-cloud/app-engine/App-Engine-Java]] — monitorizar aplicaciones de App Engine
