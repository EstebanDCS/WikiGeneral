---
title: Git y Pull Requests en el proyecto
description: "Uso de Git con Eclipse y GitHub en el proyecto Scrum de SI. Ramas por HU, Pull Requests, revisión de código, merge a main."
date: 2026-04-09
type: topic
subject: Sistemas de Información
sources: [si-guia-pull-requests.md, si-lista-verificacion-pr.md, si-git-eclipse.md]
tags: [git, github, pull-request, revision, codigo, sistemas-informacion, universidad, practicas]
---

# Git y Pull Requests en el proyecto

## Flujo de trabajo general

En el proyecto de SI cada Historia de Usuario (HU) se desarrolla en una **rama separada** (branch). Cuando la HU está lista, se abre un **Pull Request (PR)** para que un compañero revise el código antes de fusionarlo con `main`/`master`.

<svg viewBox="0 0 500 100" width="500" height="100" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <line x1="30" y1="35" x2="470" y2="35" stroke="#4a7fa5" stroke-width="2.5"/>
  <text x="14" y="39" font-size="9" fill="#4a7fa5" font-weight="bold">main</text>
  <circle cx="80" cy="35" r="5" fill="#4a7fa5"/>
  <circle cx="430" cy="35" r="5" fill="#5ba85e"/>
  <text x="416" y="26" font-size="8" fill="#5ba85e">merge</text>
  <text x="453" y="39" font-size="8" fill="currentColor" opacity="0.6">→ prod</text>
  <line x1="80" y1="35" x2="130" y2="72" stroke="#c0582a" stroke-width="1.8"/>
  <line x1="130" y1="72" x2="360" y2="72" stroke="#c0582a" stroke-width="1.8"/>
  <line x1="360" y1="72" x2="430" y2="35" stroke="#c0582a" stroke-width="1.8"/>
  <circle cx="170" cy="72" r="4" fill="#c0582a"/>
  <circle cx="240" cy="72" r="4" fill="#c0582a"/>
  <circle cx="310" cy="72" r="4" fill="#c0582a"/>
  <text x="115" y="90" font-size="8" fill="#c0582a">rama-HU-001</text>
  <rect x="340" y="56" width="36" height="18" rx="4" fill="#c0582a" opacity="0.8"/>
  <text x="358" y="69" font-size="8" fill="white" text-anchor="middle" font-weight="bold">PR</text>
  <text x="358" y="52" font-size="7" fill="currentColor" text-anchor="middle" opacity="0.7">revisión</text>
</svg>

---

## Ramas (Branches)

```bash
# Ver ramas existentes
git branch

# Crear y cambiar a una nueva rama
git checkout -b rama-HU-001

# Cambiar entre ramas
git checkout main

# Subir rama al repositorio remoto (desde Eclipse: Team > Push Branch)
git push -u origin rama-HU-001
```

---

## Pull Request — Proceso completo

### 1. Desarrollador: crear el PR

Tras hacer push de la rama al repositorio:

1. En GitHub: ir a la rama → botón **"Contribute"** → **"Open pull request"**.
2. Alternativa: pestaña **"Pull requests"** → "New pull request" → seleccionar rama origen y destino (`main`).
3. Rellenar:
   - **Título**: descriptivo (ej. "HU-001: inscripción atleta en carrera").
   - **Descripción**: qué se ha desarrollado y qué se fusiona.
   - **Reviewer**: asignar al revisor.
   - **Assignees**: asignarse a uno mismo como desarrollador.
4. Pulsar **"Create pull request"**.

### 2. Revisor: revisar el código

1. GitHub envía notificación por email/cuenta al revisor.
2. El revisor lee la descripción y va a la pestaña **"Files changed"**.
3. Por cada línea con problema: clic en `+` → escribe comentario.
   - **"Add single comment"**: comentario sin requerir acción.
   - **"Start a review"**: comentario que requiere corrección (lo más habitual).
4. Al terminar → **"Finish your review"** → elegir:
   - **Comment**: comentario general sin aprobar/rechazar.
   - **Approve**: aprueba los cambios (si todo está bien).
   - **Request changes**: solicita correcciones antes de aprobar.
5. **"Submit review"** → el desarrollador recibe notificación.

### 3. Desarrollador: corregir defectos

1. Leer cuidadosamente los comentarios del revisor.
2. Preguntar si hay dudas (en el hilo del comentario) antes de cambiar nada.
3. Implementar los cambios desde Eclipse.
4. Por cada comentario resuelto: marcar **"Resolve conversation"** en GitHub.
5. Commit + push desde Eclipse.
6. Solicitar nueva revisión: botón **"Re-request review"**.
7. Repetir hasta que el revisor apruebe.

### 4. Revisor: aprobar y fusionar

1. Cuando todo está correcto → **Approve** (opcional pero recomendado).
2. Pulsar **"Merge pull request"**.
3. Elegir **"Squash and merge"** (recomendado en SI): combina todos los commits de la rama en uno solo.
4. El PR se cierra automáticamente y la rama puede borrarse.

---

## Uso de Git desde Eclipse

### Operaciones básicas desde Eclipse

| Acción | Eclipse |
|--------|---------|
| Clonar repositorio | File > Import > Git > Clone URI |
| Ver estado | Team > Synchronize Workspace |
| Stage + Commit | Team > Commit... |
| Push | Team > Push Branch |
| Pull (fetch + merge) | Team > Pull |
| Cambiar de rama | Team > Switch To > rama |
| Ver historial | Team > Show in History |

### Flujo típico en una HU

```
1. git pull (o Team > Pull) para tener lo último de main
2. Crear rama: Team > Switch To > New Branch → "rama-HU-XXX"
3. Implementar la HU + pruebas JUnit
4. Team > Commit... → escribir mensaje descriptivo
5. Team > Push Branch → la rama aparece en GitHub
6. Abrir Pull Request en GitHub
7. (Tras revisión y aprobación) Merge en GitHub
8. Team > Switch To > main + Team > Pull para actualizar local
```

---

## Lista de verificación del PR (Checklist del revisor)

Antes de aprobar un PR, verificar:

- [ ] La descripción del PR es clara y explica qué se desarrolló.
- [ ] El código implementa lo especificado en la HU + criterios de aceptación.
- [ ] Hay pruebas JUnit para los criterios de aceptación.
- [ ] Las pruebas pasan (no hay tests en rojo).
- [ ] El código sigue las convenciones de estilo del equipo.
- [ ] No hay código comentado sin razón.
- [ ] No hay imports sin usar.
- [ ] Los nombres de variables/métodos son descriptivos.
- [ ] No hay duplicación de código (DRY).

---

## Related Topics

- [[requisitos/Historias-de-Usuario]] — cada HU → una rama → un PR
- [[metodologia/Scrum-Agile]] — Definition of Done requiere PR aprobado
- [[calidad/Pruebas-Software]] — las pruebas JUnit van incluidas en el PR
- [[calidad/Estandares-ISO]] — Pull Requests como herramienta de SCM
