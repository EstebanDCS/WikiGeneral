---
title: IAM — Roles Personalizados
description: "Google Cloud IAM: permisos, roles predefinidos vs personalizados, ciclo de vida (ALPHA→GA→DISABLED), creación por YAML y flags."
date: 2026-04-09
type: topic
subject: Google Cloud
sources: [GSP190 — IAM Custom Roles]
tags: [google-cloud, gcp, iam, roles, permisos, seguridad, personal, lab]
---

# IAM — Roles Personalizados

## ¿Qué es IAM?

**Identity and Access Management (IAM)** es el sistema de control de acceso de Google Cloud. Define **quién** puede hacer **qué** sobre **qué recursos**.

```
Política IAM = Miembro + Rol
Rol          = conjunto de Permisos
Permiso      = <servicio>.<recurso>.<verbo>
```

Ejemplo: `storage.buckets.create` → servicio `storage`, recurso `buckets`, verbo `create`.

<svg viewBox="0 0 500 120" width="500" height="120" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <rect x="10" y="30" width="100" height="60" rx="6" fill="#4a7fa5" opacity="0.3"/>
  <text x="60" y="55" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Miembro</text>
  <text x="60" y="68" font-size="8" fill="currentColor" text-anchor="middle">user: / group:</text>
  <text x="60" y="79" font-size="8" fill="currentColor" text-anchor="middle">serviceAccount:</text>
  <line x1="110" y1="60" x2="145" y2="60" stroke="currentColor" stroke-width="1.5" marker-end="url(#iam1)"/>
  <text x="127" y="55" font-size="8" fill="currentColor" text-anchor="middle">tiene</text>
  <rect x="148" y="30" width="100" height="60" rx="6" fill="#c0582a" opacity="0.3"/>
  <text x="198" y="55" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Rol</text>
  <text x="198" y="68" font-size="8" fill="currentColor" text-anchor="middle">predefinido</text>
  <text x="198" y="79" font-size="8" fill="currentColor" text-anchor="middle">personalizado</text>
  <line x1="248" y1="60" x2="283" y2="60" stroke="currentColor" stroke-width="1.5" marker-end="url(#iam1)"/>
  <text x="265" y="55" font-size="8" fill="currentColor" text-anchor="middle">contiene</text>
  <rect x="286" y="20" width="200" height="80" rx="6" fill="#5ba85e" opacity="0.2"/>
  <text x="386" y="42" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Permisos</text>
  <text x="386" y="57" font-size="8" fill="currentColor" text-anchor="middle">storage.buckets.create</text>
  <text x="386" y="68" font-size="8" fill="currentColor" text-anchor="middle">compute.instances.start</text>
  <text x="386" y="79" font-size="8" fill="currentColor" text-anchor="middle">pubsub.topics.publish</text>
  <defs>
    <marker id="iam1" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
  </defs>
</svg>

---

## Tipos de roles

| Tipo | Descripción |
|------|-------------|
| **Básicos** (legacy) | `Owner`, `Editor`, `Viewer` — muy amplios, no recomendados en producción |
| **Predefinidos** | Creados por Google para cada servicio — granulares y curados |
| **Personalizados** | Creados por el administrador — conjunto exacto de permisos necesarios |

### Principio de mínimo privilegio

Siempre conceder el **mínimo conjunto de permisos** necesario para la tarea. Los roles personalizados permiten implementarlo con precisión.

---

## Anatomía de un permiso

```
<servicio>.<recurso>.<verbo>
```

| Campo | Ejemplos |
|-------|---------|
| Servicio | `storage`, `compute`, `pubsub`, `iam`, `logging` |
| Recurso | `buckets`, `instances`, `topics`, `roles`, `sinks` |
| Verbo | `create`, `get`, `list`, `update`, `delete`, `use`, `setIamPolicy` |

Ejemplo completo: `iam.roles.create` → crear roles en IAM.

---

## Ciclo de vida de un rol personalizado

<svg viewBox="0 0 500 80" width="500" height="80" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <rect x="10" y="20" width="80" height="30" rx="5" fill="#888" opacity="0.5"/>
  <text x="50" y="39" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">ALPHA</text>
  <line x1="90" y1="35" x2="118" y2="35" stroke="currentColor" stroke-width="1.5" marker-end="url(#lc1)"/>
  <rect x="120" y="20" width="80" height="30" rx="5" fill="#5ba85e" opacity="0.5"/>
  <text x="160" y="39" font-size="10" fill="white" text-anchor="middle" font-weight="bold">GA</text>
  <line x1="200" y1="35" x2="228" y2="35" stroke="currentColor" stroke-width="1.5" marker-end="url(#lc1)"/>
  <rect x="230" y="20" width="80" height="30" rx="5" fill="#c0582a" opacity="0.5"/>
  <text x="270" y="39" font-size="10" fill="white" text-anchor="middle" font-weight="bold">DISABLED</text>
  <line x1="310" y1="35" x2="338" y2="35" stroke="currentColor" stroke-width="1.5" marker-end="url(#lc1)"/>
  <rect x="340" y="20" width="70" height="30" rx="5" fill="#444" opacity="0.5"/>
  <text x="375" y="39" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">deleted</text>
  <path d="M 375,50 Q 375,68 270,68 Q 165,68 160,50" fill="none" stroke="currentColor" stroke-width="1.2" stroke-dasharray="4,3" marker-end="url(#lc1)"/>
  <text x="268" y="78" font-size="8" fill="currentColor" text-anchor="middle" opacity="0.7">restaurar (7 días)</text>
  <defs>
    <marker id="lc1" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
  </defs>
</svg>

| Estado | Descripción |
|--------|-------------|
| **ALPHA** | En pruebas. No visible en la consola para usuarios normales. |
| **GA** (Generally Available) | Listo para producción. Visible y asignable. |
| **DISABLED** | Desactivado. Las asignaciones existentes quedan suspendidas. |
| **(deleted)** | Eliminado. Recuperable durante 7 días mediante `undelete`. |

---

## Crear un rol personalizado

### Método 1 — Fichero YAML

Crear un fichero `mi-rol.yaml`:

```yaml
title: "Mi Rol Personalizado"
description: "Acceso limitado para automatización de storage"
stage: "ALPHA"
includedPermissions:
  - storage.buckets.get
  - storage.buckets.list
  - storage.objects.create
  - storage.objects.get
  - storage.objects.list
```

```bash
# Crear el rol a nivel de proyecto
gcloud iam roles create miRolStorage \
  --project=ID_PROYECTO \
  --file=mi-rol.yaml

# Crear el rol a nivel de organización
gcloud iam roles create miRolStorage \
  --organization=ID_ORG \
  --file=mi-rol.yaml
```

### Método 2 — Flags en línea de comandos

```bash
gcloud iam roles create miRolStorage \
  --project=ID_PROYECTO \
  --title="Mi Rol Personalizado" \
  --description="Acceso limitado de storage" \
  --stage=ALPHA \
  --permissions=storage.buckets.get,storage.buckets.list,storage.objects.create
```

---

## Gestionar roles existentes

```bash
# Listar roles personalizados del proyecto
gcloud iam roles list --project=ID_PROYECTO

# Ver detalles de un rol
gcloud iam roles describe miRolStorage --project=ID_PROYECTO

# Actualizar permisos (añadir permisos)
gcloud iam roles update miRolStorage \
  --project=ID_PROYECTO \
  --add-permissions=storage.objects.delete

# Actualizar permisos (quitar permisos)
gcloud iam roles update miRolStorage \
  --project=ID_PROYECTO \
  --remove-permissions=storage.objects.delete

# Cambiar etapa del ciclo de vida
gcloud iam roles update miRolStorage \
  --project=ID_PROYECTO \
  --stage=GA

# Desactivar un rol
gcloud iam roles update miRolStorage \
  --project=ID_PROYECTO \
  --stage=DISABLED

# Eliminar un rol
gcloud iam roles delete miRolStorage --project=ID_PROYECTO

# Restaurar un rol eliminado (dentro de los 7 días)
gcloud iam roles undelete miRolStorage --project=ID_PROYECTO
```

---

## El campo `etag`

Cuando se actualiza un rol, IAM usa un campo **`etag`** (entity tag) para evitar condiciones de carrera:

1. Lees el rol → obtienes un `etag` actual.
2. Modificas el YAML con ese `etag`.
3. Envías la actualización → IAM verifica que el `etag` no haya cambiado.
4. Si otra persona actualizó el rol entre medias → el `etag` no coincide → la operación falla.

```yaml
# Ejemplo de rol con etag (obtenido de describe)
name: projects/mi-proyecto/roles/miRolStorage
title: "Mi Rol Personalizado"
etag: BwWkgwVG8kI=   # ← incluir siempre al actualizar con YAML
includedPermissions:
  - storage.buckets.get
```

---

## Asignar un rol personalizado a un miembro

```bash
# Asignar rol a usuario en un proyecto
gcloud projects add-iam-policy-binding ID_PROYECTO \
  --member=user:correo@ejemplo.com \
  --role=projects/ID_PROYECTO/roles/miRolStorage

# Asignar rol a cuenta de servicio
gcloud projects add-iam-policy-binding ID_PROYECTO \
  --member=serviceAccount:cuenta@ID_PROYECTO.iam.gserviceaccount.com \
  --role=projects/ID_PROYECTO/roles/miRolStorage

# Ver la política IAM actual del proyecto
gcloud projects get-iam-policy ID_PROYECTO
```

---

## Permisos predefinidos útiles por servicio

| Servicio | Permiso | Acción |
|---------|---------|--------|
| Cloud Storage | `storage.buckets.create` | Crear buckets |
| Cloud Storage | `storage.objects.list` | Listar objetos |
| Compute | `compute.instances.start` | Iniciar VMs |
| Compute | `compute.instances.stop` | Parar VMs |
| Pub/Sub | `pubsub.topics.publish` | Publicar mensajes |
| IAM | `iam.roles.create` | Crear roles |
| IAM | `iam.serviceAccounts.actAs` | Usar una SA como identidad |
| Logging | `logging.logEntries.create` | Escribir logs |

---

## Related Topics

- [[google-cloud/almacenamiento/Cloud-Storage]] — permisos de acceso a buckets con IAM
- [[google-cloud/funciones/Cloud-Run-Functions]] — cuentas de servicio para Cloud Functions
- [[google-cloud/monitoreo/Cloud-Monitoring]] — permisos para monitoreo y alertas
