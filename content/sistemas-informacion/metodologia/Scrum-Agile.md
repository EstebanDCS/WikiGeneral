---
title: Scrum y Métodos Ágiles
description: "Metodologías ágiles: Manifiesto Ágil, Scrum (sprints, backlog, roles, ceremonias), comparativa con cascada."
date: 2026-04-09
type: topic
subject: Sistemas de Información
sources: [si-agile-scrum.md]
tags: [scrum, agile, metodologia, sprint, backlog, sistemas-informacion, universidad]
---

# Scrum y Métodos Ágiles

## Contexto histórico

- **1940-1970**: Industria manufacturera → Toyota Production System, Just-in-Time, "waste is a crime".
- **Círculo de Deming**: Plan → Do → Check → Act. Mejora continua.
- **1950-1970**: Proceso **en cascada** (Waterfall / BDUF — Big Design Up Front).

**Problema del cascada:** se define TODO antes de construir → cambios de requisitos tardíos son muy costosos. Las pruebas llegan al final. No hay entregable hasta el final del proyecto.

---

## Manifiesto Ágil (2001)

> Individuos e interacciones **sobre** procesos y herramientas  
> Software funcionando **sobre** documentación extensiva  
> Colaboración con el cliente **sobre** negociación contractual  
> Respuesta ante el cambio **sobre** seguir un plan

---

## Procesos incrementales

En lugar de un único ciclo, el desarrollo se divide en **incrementos**:

```
Incremento 1: análisis → diseño → construcción → pruebas  → v1.0
Incremento 2: análisis → diseño → construcción → pruebas  → v2.0
...
```

- En cada incremento se entrega software funcional al cliente.
- Los requisitos se priorizan — los de mayor valor van primero.
- Si no se llega al último incremento, el cliente ya tiene versiones útiles.

---

## Scrum

Framework ágil basado en iteraciones cortas llamadas **Sprints**.

### Roles

| Rol | Función |
|-----|---------|
| **Product Owner** | Define y prioriza el Product Backlog. Voz del cliente. |
| **Scrum Master** | Facilita el proceso Scrum. Elimina impedimentos. |
| **Development Team** | Equipo auto-organizado que construye el producto. |

### Artefactos

| Artefacto | Descripción |
|-----------|-------------|
| **Product Backlog** | Lista priorizada de todas las funcionalidades del producto (HUs, EPICs). |
| **Sprint Backlog** | Subconjunto del Product Backlog seleccionado para el sprint actual. |
| **Incremento** | Software potencialmente entregable al final de cada sprint. |

### Sprints

- Duración fija: típicamente **2 semanas** (máximo 4).
- Al final de cada sprint: **Potentially Shippable Product** — software funcional.
- El equipo no cambia el sprint backlog durante el sprint.

### Ceremonias / Reuniones

| Ceremonia | Cuándo | Propósito |
|-----------|--------|-----------|
| **Sprint Planning** | Inicio del sprint | Seleccionar HUs del Product Backlog → Sprint Backlog |
| **Daily Scrum** | Cada día (~15 min) | Sincronizar: qué hice, qué haré, impedimentos |
| **Sprint Review** | Final del sprint | Demo al Product Owner + stakeholders |
| **Sprint Retrospective** | Final del sprint | Mejora del proceso del equipo |

---

## Backlog y Story Mapping

**Product Backlog:** lista unidimensional de requisitos. Los de mayor prioridad están arriba.

**Story Mapping (Jeff Patton):** estructura bidimensional:
- **Columnas** (eje X): actividades/procesos del usuario en orden cronológico.
- **Filas** (eje Y): historias dentro de cada actividad, ordenadas por prioridad.
- Agrupa las filas en **releases/sprints**.

Permite ver el "cuadro completo" del sistema y planificar releases coherentes.

---

## Burndown Chart

Gráfico que muestra el trabajo restante vs tiempo en un sprint:

```
Trabajo  ↑
restante |  \  (ideal)
         |   \
         |  · ·\·  (real)
         |        ·\·
         |___________→ días del sprint
```

Si la línea real está por encima de la ideal → el equipo va retrasado.

---

## Scrum en Sistemas de Información

En la asignatura el proyecto se divide en **3 Sprints**:
- **Sprint 1**: Las HUs vienen dadas (familiarización).
- **Sprint 2 y 3**: El equipo elabora las HUs a partir de los requisitos de usuario.

Caso de estudio: aplicación de gestión de **carreras populares** (inscripción, pago, cronometraje, resultados).

---

## Related Topics

- [[requisitos/Historias-de-Usuario]] — elaboración del Sprint Backlog con HUs
- [[requisitos/Requisitos-Software]] — de los requisitos de usuario al Sprint Backlog
- [[practicas/Git-y-Pull-Requests]] — gestión del código en el proyecto Scrum
