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

<svg viewBox="0 0 520 160" width="520" height="160" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <text x="4" y="38" font-size="10" fill="currentColor" opacity="0.7">Inc. 1</text>
  <text x="4" y="88" font-size="10" fill="currentColor" opacity="0.7">Inc. 2</text>
  <text x="4" y="138" font-size="10" fill="currentColor" opacity="0.7">Inc. 3</text>
  <rect x="44" y="18" width="72" height="28" rx="4" fill="#4a7fa5" opacity="0.6"/>
  <text x="80" y="37" font-size="9" fill="white" text-anchor="middle">Análisis</text>
  <rect x="124" y="18" width="72" height="28" rx="4" fill="#4a7fa5" opacity="0.6"/>
  <text x="160" y="37" font-size="9" fill="white" text-anchor="middle">Diseño</text>
  <rect x="204" y="18" width="72" height="28" rx="4" fill="#4a7fa5" opacity="0.6"/>
  <text x="240" y="37" font-size="9" fill="white" text-anchor="middle">Construcción</text>
  <rect x="284" y="18" width="72" height="28" rx="4" fill="#4a7fa5" opacity="0.6"/>
  <text x="320" y="37" font-size="9" fill="white" text-anchor="middle">Pruebas</text>
  <rect x="368" y="18" width="50" height="28" rx="4" fill="#5ba85e" opacity="0.8"/>
  <text x="393" y="37" font-size="9" fill="white" text-anchor="middle">v1.0 ✓</text>
  <rect x="44" y="68" width="72" height="28" rx="4" fill="#c0582a" opacity="0.5"/>
  <text x="80" y="87" font-size="9" fill="white" text-anchor="middle">Análisis</text>
  <rect x="124" y="68" width="72" height="28" rx="4" fill="#c0582a" opacity="0.5"/>
  <text x="160" y="87" font-size="9" fill="white" text-anchor="middle">Diseño</text>
  <rect x="204" y="68" width="72" height="28" rx="4" fill="#c0582a" opacity="0.5"/>
  <text x="240" y="87" font-size="9" fill="white" text-anchor="middle">Construcción</text>
  <rect x="284" y="68" width="72" height="28" rx="4" fill="#c0582a" opacity="0.5"/>
  <text x="320" y="87" font-size="9" fill="white" text-anchor="middle">Pruebas</text>
  <rect x="368" y="68" width="50" height="28" rx="4" fill="#5ba85e" opacity="0.8"/>
  <text x="393" y="87" font-size="9" fill="white" text-anchor="middle">v2.0 ✓</text>
  <rect x="44" y="118" width="72" height="28" rx="4" fill="#888" opacity="0.4"/>
  <text x="80" y="137" font-size="9" fill="currentColor" text-anchor="middle">Análisis</text>
  <rect x="124" y="118" width="72" height="28" rx="4" fill="#888" opacity="0.4"/>
  <text x="160" y="137" font-size="9" fill="currentColor" text-anchor="middle">Diseño</text>
  <rect x="204" y="118" width="72" height="28" rx="4" fill="#888" opacity="0.4"/>
  <text x="240" y="137" font-size="9" fill="currentColor" text-anchor="middle">Construcción</text>
  <rect x="284" y="118" width="72" height="28" rx="4" fill="#888" opacity="0.4"/>
  <text x="320" y="137" font-size="9" fill="currentColor" text-anchor="middle">Pruebas</text>
  <rect x="368" y="118" width="50" height="28" rx="4" fill="#888" opacity="0.4"/>
  <text x="393" y="137" font-size="9" fill="currentColor" text-anchor="middle">v3.0…</text>
</svg>

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

<svg viewBox="0 0 340 180" width="340" height="180" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <line x1="50" y1="20" x2="50" y2="150" stroke="currentColor" stroke-width="1.5"/>
  <line x1="50" y1="150" x2="310" y2="150" stroke="currentColor" stroke-width="1.5"/>
  <text x="10" y="90" font-size="9" fill="currentColor" text-anchor="middle" transform="rotate(-90,10,90)">Trabajo restante</text>
  <text x="180" y="168" font-size="9" fill="currentColor" text-anchor="middle">Días del sprint</text>
  <text x="50" y="162" font-size="8" fill="currentColor" text-anchor="middle">0</text>
  <text x="180" y="162" font-size="8" fill="currentColor" text-anchor="middle">7</text>
  <text x="310" y="162" font-size="8" fill="currentColor" text-anchor="middle">14</text>
  <text x="44" y="24" font-size="8" fill="currentColor" text-anchor="end">100%</text>
  <text x="44" y="150" font-size="8" fill="currentColor" text-anchor="end">0%</text>
  <line x1="50" y1="22" x2="310" y2="150" stroke="#4a7fa5" stroke-width="2" stroke-dasharray="6,3"/>
  <text x="270" y="100" font-size="9" fill="#4a7fa5">ideal</text>
  <polyline points="50,22 100,55 130,70 160,100 190,108 220,118 260,128 310,150" fill="none" stroke="#c0582a" stroke-width="2"/>
  <circle cx="50" cy="22" r="3" fill="#c0582a"/>
  <circle cx="100" cy="55" r="3" fill="#c0582a"/>
  <circle cx="130" cy="70" r="3" fill="#c0582a"/>
  <circle cx="160" cy="100" r="3" fill="#c0582a"/>
  <circle cx="220" cy="118" r="3" fill="#c0582a"/>
  <circle cx="310" cy="150" r="3" fill="#c0582a"/>
  <text x="140" y="56" font-size="9" fill="#c0582a">real</text>
</svg>

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
