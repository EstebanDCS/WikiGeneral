---
title: Cloud Run Functions
description: "Google Cloud Run Functions (antes Cloud Functions): funciones event-driven y HTTPS, despliegue por CLI y consola, Pub/Sub trigger, Node.js."
date: 2026-04-09
type: topic
subject: Google Cloud
sources: [GSP080 — Cloud Run Functions Qwik Start CLI, GSP081 — Cloud Run Functions Qwik Start Console]
tags: [google-cloud, gcp, cloud-functions, cloud-run, serverless, pubsub, nodejs, personal, lab]
---

# Cloud Run Functions

## ¿Qué son?

**Cloud Run Functions** (anteriormente Cloud Functions) es la plataforma **serverless** de Google Cloud para ejecutar código en respuesta a eventos. No hay que aprovisionar ni gestionar servidores: el código escala automáticamente desde 0 hasta miles de instancias.

<svg viewBox="0 0 500 130" width="500" height="130" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <!-- Trigger -->
  <rect x="10" y="40" width="110" height="50" rx="6" fill="#4a7fa5" opacity="0.3"/>
  <text x="65" y="62" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Disparador</text>
  <text x="65" y="75" font-size="8" fill="currentColor" text-anchor="middle">HTTP / Pub/Sub</text>
  <text x="65" y="86" font-size="8" fill="currentColor" text-anchor="middle">Cloud Storage / etc.</text>
  <!-- Arrow -->
  <line x1="120" y1="65" x2="158" y2="65" stroke="currentColor" stroke-width="1.5" marker-end="url(#crf1)"/>
  <!-- Function box -->
  <rect x="160" y="25" width="150" height="80" rx="6" fill="#c0582a" opacity="0.25"/>
  <text x="235" y="50" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Cloud Run Function</text>
  <text x="235" y="65" font-size="8" fill="currentColor" text-anchor="middle">Node.js / Python / Go</text>
  <text x="235" y="76" font-size="8" fill="currentColor" text-anchor="middle">Java / .NET / Ruby</text>
  <text x="235" y="87" font-size="8" fill="currentColor" text-anchor="middle">Escala automático</text>
  <!-- Arrow -->
  <line x1="310" y1="65" x2="348" y2="65" stroke="currentColor" stroke-width="1.5" marker-end="url(#crf1)"/>
  <!-- Output -->
  <rect x="350" y="40" width="130" height="50" rx="6" fill="#5ba85e" opacity="0.2"/>
  <text x="415" y="60" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Acción / Respuesta</text>
  <text x="415" y="73" font-size="8" fill="currentColor" text-anchor="middle">HTTP response</text>
  <text x="415" y="84" font-size="8" fill="currentColor" text-anchor="middle">escribe en BD / Storage</text>
  <defs>
    <marker id="crf1" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
  </defs>
</svg>

---

## Tipos de funciones

| Tipo | Disparador | Cuándo usarlas |
|------|-----------|----------------|
| **HTTP** | Petición HTTP/HTTPS directa | APIs REST, webhooks, formularios |
| **Evento** (CloudEvent) | Pub/Sub, Cloud Storage, Firestore… | Procesamiento asíncrono, pipelines |

---

## Lab GSP080 — Despliegue por CLI (Disparador Pub/Sub)

### Código de ejemplo — Node.js

```javascript
// index.js — función con trigger Pub/Sub
const functions = require('@google-cloud/functions-framework');

functions.cloudEvent('helloPubSub', cloudEvent => {
  // El mensaje llega codificado en base64
  const base64name = cloudEvent.data.message.data;
  const name = base64name
    ? Buffer.from(base64name, 'base64').toString()
    : 'World';
  
  console.log(`Hola ${name}!`);
});
```

```json
// package.json
{
  "name": "hola-pubsub",
  "version": "1.0.0",
  "dependencies": {
    "@google-cloud/functions-framework": "^3.0.0"
  }
}
```

### Desplegar desde la CLI

```bash
# Desplegar función con disparador Pub/Sub
gcloud functions deploy helloPubSub \
  --gen2 \
  --runtime=nodejs20 \
  --region=us-east1 \
  --source=. \
  --entry-point=helloPubSub \
  --trigger-topic=MI_TOPICO

# Crear el topic de Pub/Sub si no existe
gcloud pubsub topics create MI_TOPICO
```

### Probar la función (publicar en Pub/Sub)

```bash
# Publicar un mensaje en el topic (activa la función)
gcloud pubsub topics publish MI_TOPICO \
  --message="Mundo"

# Ver los logs de la función
gcloud functions logs read helloPubSub \
  --region=us-east1
```

---

## Lab GSP081 — Despliegue por Consola (Disparador HTTP)

### Código de ejemplo — Node.js HTTPS

```javascript
// index.js — función HTTP
const functions = require('@google-cloud/functions-framework');

functions.http('helloHttp', (req, res) => {
  res.send(`Hola ${req.query.name || req.body.name || 'World'}!`);
});
```

### Pasos en la consola de GCP

1. Ir a **Cloud Run** → **Functions** → **Escribir una función**.
2. Configurar:
   - **Entorno**: 2ª generación (recomendado).
   - **Nombre de la función**: `hola-http`.
   - **Región**: la más cercana al usuario.
   - **Disparador**: HTTPS.
   - **Autenticación**: "Allow unauthenticated invocations" (para pruebas).
3. En el editor de código: pegar el código, asegurarse de que `package.json` tiene las dependencias.
4. Pulsar **Desplegar**.

### Probar la función HTTP

```bash
# Obtener la URL de la función desplegada
gcloud functions describe hola-http \
  --region=us-east1 \
  --gen2 \
  --format="value(serviceConfig.uri)"

# Llamar a la función con curl
curl "https://REGION-PROYECTO.cloudfunctions.net/hola-http?name=GCP"
```

---

## Comandos CLI generales

```bash
# Listar todas las funciones del proyecto
gcloud functions list

# Ver detalles de una función
gcloud functions describe NOMBRE_FUNCION --region=REGION

# Ver logs en tiempo real
gcloud functions logs read NOMBRE_FUNCION \
  --region=REGION \
  --limit=50

# Eliminar una función
gcloud functions delete NOMBRE_FUNCION \
  --region=REGION \
  --gen2

# Actualizar una función (mismo comando que deploy)
gcloud functions deploy NOMBRE_FUNCION \
  --gen2 \
  --runtime=nodejs20 \
  --region=REGION \
  --source=. \
  --entry-point=PUNTO_ENTRADA
```

---

## Arquitectura interna (2ª generación)

Las funciones de 2ª generación corren sobre **Cloud Run** internamente. Esto da:
- Tiempos de ejecución más largos (hasta 60 minutos).
- Concurrencia configurable (múltiples peticiones por instancia).
- Más control sobre CPU y memoria.
- Acceso a la VPC de la organización.

```
Petición → Cloud Run (contenedor Docker gestionado) → Código de la función
```

---

## Integración con Pub/Sub

<svg viewBox="0 0 500 100" width="500" height="100" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <!-- Publisher -->
  <rect x="10" y="25" width="90" height="50" rx="5" fill="#4a7fa5" opacity="0.3"/>
  <text x="55" y="48" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">Publicador</text>
  <text x="55" y="61" font-size="8" fill="currentColor" text-anchor="middle">gcloud pubsub</text>
  <text x="55" y="72" font-size="8" fill="currentColor" text-anchor="middle">topics publish</text>
  <!-- Arrow to topic -->
  <line x1="100" y1="50" x2="135" y2="50" stroke="currentColor" stroke-width="1.5" marker-end="url(#ps1)"/>
  <!-- Topic -->
  <rect x="138" y="25" width="110" height="50" rx="5" fill="#c0582a" opacity="0.25"/>
  <text x="193" y="48" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">Pub/Sub Topic</text>
  <text x="193" y="61" font-size="8" fill="currentColor" text-anchor="middle">cola de mensajes</text>
  <text x="193" y="72" font-size="8" fill="currentColor" text-anchor="middle">persistente</text>
  <!-- Arrow to function -->
  <line x1="248" y1="50" x2="283" y2="50" stroke="currentColor" stroke-width="1.5" marker-end="url(#ps1)"/>
  <!-- Function -->
  <rect x="286" y="25" width="100" height="50" rx="5" fill="#5ba85e" opacity="0.25"/>
  <text x="336" y="45" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">Cloud Run</text>
  <text x="336" y="56" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">Function</text>
  <text x="336" y="69" font-size="8" fill="currentColor" text-anchor="middle">procesa mensaje</text>
  <!-- Arrow to action -->
  <line x1="386" y1="50" x2="421" y2="50" stroke="currentColor" stroke-width="1.5" marker-end="url(#ps1)"/>
  <!-- Action -->
  <rect x="424" y="30" width="65" height="40" rx="5" fill="#888" opacity="0.2"/>
  <text x="456" y="48" font-size="8" fill="currentColor" text-anchor="middle">Acción</text>
  <text x="456" y="59" font-size="8" fill="currentColor" text-anchor="middle">log / BD / API</text>
  <defs>
    <marker id="ps1" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
  </defs>
</svg>

**Pub/Sub** actúa de buffer asíncrono entre el publicador y la función. La función no necesita estar activa en el momento de la publicación — se activa automáticamente al recibir el mensaje.

---

## Variables de entorno y secretos

```bash
# Desplegar con variables de entorno
gcloud functions deploy FUNCION \
  --gen2 \
  --set-env-vars=DB_HOST=10.0.0.1,ENV=prod \
  --region=REGION

# Usar Secret Manager para secretos
gcloud functions deploy FUNCION \
  --gen2 \
  --set-secrets=API_KEY=projects/PROYECTO/secrets/api-key:latest \
  --region=REGION
```

---

## Related Topics

- [[google-cloud/iam/IAM-Roles-Personalizados]] — cuentas de servicio y permisos para funciones
- [[google-cloud/monitoreo/Cloud-Monitoring]] — monitorizar ejecuciones y errores
- [[google-cloud/monitoreo/Metricas-Basadas-en-Logs]] — crear alertas basadas en logs de funciones
