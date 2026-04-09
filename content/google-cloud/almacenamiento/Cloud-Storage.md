---
title: Cloud Storage
description: "Google Cloud Storage: buckets, objetos, clases de almacenamiento, ACLs, acceso público y comandos CLI con gcloud storage."
date: 2026-04-09
type: topic
subject: Google Cloud
sources: [GSP074 — Cloud Storage Qwik Start CLI/SDK]
tags: [google-cloud, gcp, cloud-storage, buckets, acl, cli, personal, lab]
---

# Cloud Storage

## ¿Qué es Cloud Storage?

Cloud Storage es el servicio de almacenamiento de **objetos** de Google Cloud. A diferencia de un sistema de ficheros, los datos se organizan en **buckets** (cubos) que contienen **objetos** (ficheros + metadatos). Es global, duradero (99.999999999% — 11 nueves) y se integra con prácticamente todos los servicios de GCP.

<svg viewBox="0 0 500 130" width="500" height="130" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <!-- GCP Project box -->
  <rect x="10" y="10" width="480" height="110" rx="8" fill="none" stroke="currentColor" stroke-width="1.5" stroke-dasharray="6,3" opacity="0.4"/>
  <text x="25" y="28" font-size="9" fill="currentColor" opacity="0.6">Proyecto GCP</text>
  <!-- Bucket -->
  <rect x="40" y="40" width="120" height="65" rx="6" fill="#4a7fa5" opacity="0.25"/>
  <text x="100" y="60" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Bucket</text>
  <text x="100" y="73" font-size="8" fill="currentColor" text-anchor="middle" opacity="0.7">nombre único global</text>
  <text x="100" y="84" font-size="8" fill="currentColor" text-anchor="middle" opacity="0.7">región / multi-región</text>
  <text x="100" y="95" font-size="8" fill="currentColor" text-anchor="middle" opacity="0.7">clase de almacenamiento</text>
  <!-- Objects inside bucket -->
  <rect x="200" y="40" width="260" height="65" rx="6" fill="#5ba85e" opacity="0.15"/>
  <text x="330" y="58" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Objetos</text>
  <rect x="215" y="65" width="70" height="26" rx="3" fill="#5ba85e" opacity="0.4"/>
  <text x="250" y="82" font-size="9" fill="currentColor" text-anchor="middle">foto.jpg</text>
  <rect x="295" y="65" width="70" height="26" rx="3" fill="#5ba85e" opacity="0.4"/>
  <text x="330" y="82" font-size="9" fill="currentColor" text-anchor="middle">datos.csv</text>
  <rect x="375" y="65" width="70" height="26" rx="3" fill="#5ba85e" opacity="0.4"/>
  <text x="410" y="82" font-size="9" fill="currentColor" text-anchor="middle">modelo.pkl</text>
  <!-- Arrow from bucket to objects -->
  <line x1="160" y1="72" x2="198" y2="72" stroke="currentColor" stroke-width="1.5" marker-end="url(#acs)"/>
  <defs>
    <marker id="acs" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
  </defs>
</svg>

---

## Clases de almacenamiento

| Clase | SLA disponibilidad | Mínimo de almacenamiento | Uso |
|-------|--------------------|--------------------------|-----|
| **Standard** | 99.99% (región) | Sin mínimo | Datos de acceso frecuente |
| **Nearline** | 99.9% | 30 días | Acceso ~1 vez al mes |
| **Coldline** | 99.9% | 90 días | Acceso ~1 vez al trimestre |
| **Archive** | 99.9% | 365 días | Backups a largo plazo |

La clase afecta al **coste de almacenamiento y de recuperación**: Standard es más caro de almacenar pero recuperación gratuita; Archive es muy barato de almacenar pero caro de recuperar.

---

## Comandos CLI — `gcloud storage`

### Crear y gestionar buckets

```bash
# Crear un bucket (nombre único globalmente)
gcloud storage buckets create gs://NOMBRE_BUCKET \
  --location=us-east1 \
  --storage-class=STANDARD

# Listar todos los buckets del proyecto
gcloud storage buckets list

# Obtener información de un bucket
gcloud storage buckets describe gs://NOMBRE_BUCKET

# Eliminar un bucket vacío
gcloud storage buckets delete gs://NOMBRE_BUCKET
```

> El nombre del bucket debe ser **único a nivel mundial** en todo Google Cloud. Usa el ID del proyecto como prefijo para evitar colisiones.

### Subir y descargar objetos

```bash
# Subir un fichero al bucket
gcloud storage cp fichero_local.txt gs://NOMBRE_BUCKET/

# Subir con ruta de destino específica
gcloud storage cp datos.csv gs://NOMBRE_BUCKET/carpeta/datos.csv

# Subir un directorio completo (recursivo)
gcloud storage cp -r ./mi_directorio gs://NOMBRE_BUCKET/

# Descargar un objeto
gcloud storage cp gs://NOMBRE_BUCKET/fichero.txt ./destino_local.txt

# Copiar entre buckets
gcloud storage cp gs://BUCKET_ORIGEN/obj.txt gs://BUCKET_DESTINO/
```

### Listar y gestionar objetos

```bash
# Listar objetos en un bucket
gcloud storage ls gs://NOMBRE_BUCKET/

# Listar con detalles (tamaño, fecha)
gcloud storage ls -l gs://NOMBRE_BUCKET/

# Listar recursivamente
gcloud storage ls -r gs://NOMBRE_BUCKET/**

# Mover/renombrar objeto
gcloud storage mv gs://BUCKET/origen.txt gs://BUCKET/destino.txt

# Eliminar un objeto
gcloud storage rm gs://NOMBRE_BUCKET/fichero.txt

# Eliminar todos los objetos de un bucket
gcloud storage rm -r gs://NOMBRE_BUCKET/**
```

### Sincronizar directorios

```bash
# Sincronizar directorio local con bucket (sólo sube cambios)
gcloud storage rsync ./local gs://NOMBRE_BUCKET/

# Sincronizar eliminando objetos remotos que no existen en local
gcloud storage rsync -d ./local gs://NOMBRE_BUCKET/
```

---

## Control de Acceso (ACLs)

Cloud Storage tiene dos modelos de control de acceso:

| Modelo | Descripción |
|--------|-------------|
| **IAM uniforme** (recomendado) | Permisos a nivel de bucket mediante IAM. Sin ACLs por objeto. |
| **ACL (legacy)** | Permisos granulares por objeto. Menos recomendado. |

### ACLs predefinidas

```bash
# Ver ACLs de un objeto
gcloud storage objects describe gs://BUCKET/objeto.txt --format="json(acl)"

# Hacer un objeto público (lectura para todos)
gcloud storage objects update gs://BUCKET/objeto.txt \
  --predefined-acl=publicRead

# Hacer todos los objetos de un bucket públicos
gcloud storage buckets update gs://BUCKET \
  --predefined-default-object-acl=publicRead
```

### ACL uniforme a nivel de bucket (recomendado)

```bash
# Activar acceso uniforme en el bucket
gcloud storage buckets update gs://BUCKET \
  --uniform-bucket-level-access

# Dar acceso de lectura a un usuario
gcloud storage buckets add-iam-policy-binding gs://BUCKET \
  --member=user:correo@ejemplo.com \
  --role=roles/storage.objectViewer

# Dar acceso de lectura al público (todos)
gcloud storage buckets add-iam-policy-binding gs://BUCKET \
  --member=allUsers \
  --role=roles/storage.objectViewer
```

---

## Acceso público y URLs

Cuando un objeto es público, su URL de acceso es:

```
https://storage.googleapis.com/NOMBRE_BUCKET/NOMBRE_OBJETO
```

Para buckets configurados como **sitios web estáticos**, se puede usar un dominio personalizado o la URL del servicio.

---

## Permisos IAM útiles para Cloud Storage

| Rol | Descripción |
|-----|-------------|
| `roles/storage.admin` | Control total sobre buckets y objetos |
| `roles/storage.objectAdmin` | CRUD sobre objetos (no gestión de buckets) |
| `roles/storage.objectCreator` | Solo puede subir objetos |
| `roles/storage.objectViewer` | Solo puede listar y descargar objetos |

---

## Metadatos de objetos

Cada objeto lleva metadatos clave-valor:

```bash
# Consultar metadatos de un objeto
gcloud storage objects describe gs://BUCKET/objeto.txt

# Establecer content-type
gcloud storage objects update gs://BUCKET/objeto.txt \
  --content-type=text/html

# Añadir metadatos personalizados
gcloud storage objects update gs://BUCKET/objeto.txt \
  --custom-metadata=version=2,env=prod
```

---

## Ciclo de vida de objetos

Se pueden configurar **reglas de ciclo de vida** para automatizar la transición de clase o el borrado:

```json
{
  "rule": [
    {
      "action": {"type": "SetStorageClass", "storageClass": "NEARLINE"},
      "condition": {"age": 30}
    },
    {
      "action": {"type": "Delete"},
      "condition": {"age": 365}
    }
  ]
}
```

```bash
# Aplicar política de ciclo de vida al bucket
gcloud storage buckets update gs://BUCKET \
  --lifecycle-file=lifecycle.json
```

---

## Related Topics

- [[google-cloud/iam/IAM-Roles-Personalizados]] — control de acceso con IAM
- [[google-cloud/monitoreo/Cloud-Monitoring]] — monitorizar el uso de Cloud Storage
- [[google-cloud/app-engine/App-Engine-Java]] — App Engine puede servir contenido desde Cloud Storage
