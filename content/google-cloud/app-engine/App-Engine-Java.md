---
title: App Engine — Java (Entorno Estándar)
description: "Google App Engine entorno estándar con Java: estructura de proyecto, despliegue con gcloud app deploy, escalado automático, HTTP server básico."
date: 2026-04-09
type: topic
subject: Google Cloud
sources: [GSP068 — App Engine Qwik Start Java]
tags: [google-cloud, gcp, app-engine, java, serverless, despliegue, personal, lab]
---

# App Engine — Java (Entorno Estándar)

## ¿Qué es App Engine?

**App Engine** es la plataforma **PaaS** (Platform as a Service) de Google Cloud para desplegar aplicaciones web sin gestionar infraestructura. El desarrollador sube el código; Google gestiona el hardware, el SO, el escalado y la disponibilidad.

<svg viewBox="0 0 500 120" width="500" height="120" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <rect x="10" y="30" width="90" height="60" rx="6" fill="#4a7fa5" opacity="0.3"/>
  <text x="55" y="56" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Desarrollador</text>
  <text x="55" y="70" font-size="8" fill="currentColor" text-anchor="middle">código + config</text>
  <text x="55" y="81" font-size="8" fill="currentColor" text-anchor="middle">app.yaml</text>
  <line x1="100" y1="60" x2="135" y2="60" stroke="currentColor" stroke-width="1.5" marker-end="url(#ae1)"/>
  <text x="117" y="53" font-size="8" fill="currentColor" text-anchor="middle">deploy</text>
  <rect x="138" y="15" width="180" height="90" rx="6" fill="#c0582a" opacity="0.2"/>
  <text x="228" y="38" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">App Engine</text>
  <text x="228" y="52" font-size="8" fill="currentColor" text-anchor="middle">Entorno de ejecución Java</text>
  <text x="228" y="64" font-size="8" fill="currentColor" text-anchor="middle">Escalado automático</text>
  <text x="228" y="76" font-size="8" fill="currentColor" text-anchor="middle">Load balancing integrado</text>
  <text x="228" y="88" font-size="8" fill="currentColor" text-anchor="middle">HTTPS automático</text>
  <line x1="318" y1="60" x2="353" y2="60" stroke="currentColor" stroke-width="1.5" marker-end="url(#ae1)"/>
  <rect x="356" y="30" width="130" height="60" rx="6" fill="#5ba85e" opacity="0.2"/>
  <text x="421" y="53" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Usuarios</text>
  <text x="421" y="66" font-size="8" fill="currentColor" text-anchor="middle">PROYECTO.REGION.r.appspot.com</text>
  <text x="421" y="77" font-size="8" fill="currentColor" text-anchor="middle">dominio personalizado</text>
  <defs>
    <marker id="ae1" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
  </defs>
</svg>

---

## Entorno Estándar vs Flexible

| Característica | Estándar | Flexible |
|---------------|---------|----------|
| **Arranque en frío** | Muy rápido (segundos) | Más lento (minutos) |
| **Escalado a cero** | Sí — 0 instancias cuando no hay tráfico | No |
| **Runtimes** | Java, Python, Go, Node.js, PHP, Ruby (versiones específicas) | Cualquier contenedor Docker |
| **Acceso a internet** | Restringido (solo servicios GCP por defecto) | Completo |
| **Precio** | Por instancia-hora (escala a 0) | Por vCPU-hora y RAM |
| **Timeouts** | 10 min (tareas 24h) | Sin límite |

---

## Estructura de un proyecto Java para App Engine

```
mi-app/
├── src/
│   └── main/
│       ├── java/
│       │   └── com/ejemplo/
│       │       └── HelloAppEngine.java   ← servlet o handler HTTP
│       └── webapp/
│           └── WEB-INF/
│               └── appengine-web.xml     ← config App Engine (legacy)
├── app.yaml                              ← config principal (recomendado)
└── pom.xml                               ← dependencias Maven
```

### app.yaml — Configuración principal

```yaml
runtime: java17           # versión de Java
env: standard             # entorno estándar

instance_class: F2        # clase de instancia (F1 = gratis, F2 = 256MB RAM)

automatic_scaling:
  min_idle_instances: 0   # instancias en reposo (0 = escala a cero)
  max_idle_instances: 1
  min_pending_latency: 30ms
  max_pending_latency: automatic
  max_concurrent_requests: 50

handlers:
  - url: /.*
    script: auto          # App Engine gestiona el routing
```

---

## Código de ejemplo — Servidor HTTP básico

### Usando el SDK de Java para App Engine (framework propio)

```java
// HelloAppEngine.java
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name = "HelloAppEngine", value = "/")
public class HelloAppEngine extends HttpServlet {

  @Override
  public void doGet(HttpServletRequest request, HttpServletResponse response)
      throws IOException {
    response.setContentType("text/plain;charset=UTF-8");
    response.getWriter().println("¡Hola desde App Engine!");
  }
}
```

### pom.xml (dependencias mínimas)

```xml
<dependencies>
  <!-- Servlet API -->
  <dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
  </dependency>
  <!-- App Engine SDK (opcional para APIs nativas) -->
  <dependency>
    <groupId>com.google.appengine</groupId>
    <artifactId>appengine-api-1.0-sdk</artifactId>
    <version>2.0.10</version>
  </dependency>
</dependencies>
```

---

## Despliegue con `gcloud`

```bash
# Inicializar App Engine en el proyecto (solo la primera vez)
gcloud app create --region=us-central

# Desplegar la aplicación
gcloud app deploy

# Desplegar indicando fichero app.yaml explícitamente
gcloud app deploy app.yaml

# Desplegar sin prompts de confirmación
gcloud app deploy --quiet

# Desplegar una versión específica sin dirigir tráfico a ella
gcloud app deploy --no-promote --version=v2

# Ver la aplicación en el navegador
gcloud app browse

# Ver logs en tiempo real
gcloud app logs tail -s default
```

---

## Gestión de versiones y tráfico

App Engine permite tener **múltiples versiones** desplegadas simultáneamente:

```bash
# Listar versiones
gcloud app versions list

# Dirigir todo el tráfico a una versión específica
gcloud app services set-traffic default \
  --splits=v2=1

# Dividir tráfico entre versiones (A/B testing)
gcloud app services set-traffic default \
  --splits=v1=0.3,v2=0.7

# Parar una versión (deja de recibir tráfico y escala a 0)
gcloud app versions stop v1

# Eliminar una versión
gcloud app versions delete v1
```

---

## URL de la aplicación

Cada aplicación de App Engine tiene una URL automática:

```
https://ID_PROYECTO.REGION.r.appspot.com
```

O simplemente:

```
https://ID_PROYECTO.appspot.com
```

Se puede configurar un **dominio personalizado** en la consola: **App Engine** → **Settings** → **Custom domains**.

---

## Logs y monitoreo

```bash
# Ver logs de la aplicación
gcloud app logs read --service=default --limit=50

# Filtrar por severidad
gcloud app logs read --service=default \
  --level=error \
  --limit=20

# Ver en tiempo real
gcloud app logs tail
```

Los logs también están disponibles en **Cloud Logging** bajo `resource.type="gae_app"`.

---

## Variables de entorno

```yaml
# En app.yaml
env_variables:
  DB_HOST: "10.0.0.1"
  ENV: "production"
  API_KEY: "projects/mi-proyecto/secrets/api-key/versions/latest"
```

Para secretos, es mejor referenciarlos desde **Secret Manager** y acceder desde el código mediante la API.

---

## Comparativa con otras plataformas serverless de GCP

| Plataforma | Caso de uso ideal |
|------------|-----------------|
| **App Engine Standard** | Aplicaciones web de larga duración, APIs REST con tráfico variable |
| **Cloud Run** | Contenedores Docker, microservicios, tráfico bajo con escala a 0 |
| **Cloud Run Functions** | Funciones pequeñas y de corta duración, event-driven |
| **GKE** | Aplicaciones con requisitos complejos de orquestación, estado |

---

## Related Topics

- [[google-cloud/monitoreo/Cloud-Monitoring]] — monitorizar el rendimiento de App Engine
- [[google-cloud/monitoreo/Metricas-Basadas-en-Logs]] — alertas basadas en logs de App Engine
- [[google-cloud/funciones/Cloud-Run-Functions]] — alternativa serverless orientada a eventos
- [[google-cloud/iam/IAM-Roles-Personalizados]] — cuentas de servicio para App Engine
