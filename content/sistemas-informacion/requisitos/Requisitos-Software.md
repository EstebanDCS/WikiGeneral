---
title: Requisitos del Software
description: "Requisitos del software: definición, niveles (negocio/usuario/sistema), tipos (funcionales/no funcionales), características, proceso de ingeniería de requisitos."
date: 2026-04-09
type: topic
subject: Sistemas de Información
sources: [si-requisitos-software.md]
tags: [requisitos, ingenieria-requisitos, funcional, no-funcional, sistemas-informacion, universidad]
---

# Requisitos del Software

## Por qué son críticos

> "Lo más complicado de construir un sistema software es decidir exactamente qué construir. Ninguna otra parte del trabajo afecta tan negativamente al resultado si se hace mal. Ninguna otra es tan difícil de rectificar." — F.P. Brooks

Los requisitos definen el **"qué"** del sistema, no el "cómo". Los errores en requisitos son los más costosos de corregir (crecen exponencialmente con el tiempo).

---

## Definición

**Requisito** (ISO/IEC/IEEE 29148): condición o capacidad que debe poseer un sistema para satisfacer un contrato, estándar o especificación. Describe:

- **Qué debe hacer** el software (capacidades/funciones).
- **Qué debe ser** el software (características de calidad).
- **Qué limitaciones** tendrá (restricciones).

---

## Niveles de requisitos

<svg viewBox="0 0 480 140" width="480" height="140" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <rect x="10" y="10" width="80" height="32" rx="4" fill="#4a7fa5" opacity="0.7"/>
  <text x="50" y="30" font-size="10" fill="white" text-anchor="middle" font-weight="bold">Negocio</text>
  <line x1="90" y1="26" x2="115" y2="26" stroke="currentColor" stroke-width="1.5" marker-end="url(#ra)"/>
  <rect x="117" y="10" width="340" height="32" rx="4" fill="#4a7fa5" opacity="0.3"/>
  <text x="287" y="26" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Requisitos de Negocio</text>
  <text x="287" y="38" font-size="8" fill="currentColor" text-anchor="middle">objetivos de la organización</text>
  <line x1="50" y1="42" x2="50" y2="58" stroke="currentColor" stroke-width="1.5" marker-end="url(#ra)"/>
  <rect x="10" y="60" width="80" height="32" rx="4" fill="#c0582a" opacity="0.6"/>
  <text x="50" y="80" font-size="10" fill="white" text-anchor="middle" font-weight="bold">Usuario</text>
  <line x1="90" y1="76" x2="115" y2="76" stroke="currentColor" stroke-width="1.5" marker-end="url(#ra)"/>
  <rect x="117" y="60" width="340" height="32" rx="4" fill="#c0582a" opacity="0.2"/>
  <text x="287" y="76" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Requisitos de Usuario</text>
  <text x="287" y="88" font-size="8" fill="currentColor" text-anchor="middle">qué necesitan los stakeholders (pliego, EPICs)</text>
  <line x1="50" y1="92" x2="50" y2="108" stroke="currentColor" stroke-width="1.5" marker-end="url(#ra)"/>
  <rect x="10" y="110" width="80" height="22" rx="4" fill="#5ba85e" opacity="0.7"/>
  <text x="50" y="125" font-size="10" fill="white" text-anchor="middle" font-weight="bold">Sistema</text>
  <line x1="90" y1="121" x2="115" y2="121" stroke="currentColor" stroke-width="1.5" marker-end="url(#ra)"/>
  <rect x="117" y="110" width="340" height="22" rx="4" fill="#5ba85e" opacity="0.2"/>
  <text x="287" y="125" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Requisitos del Sistema — Backlog (HUs + criterios aceptación)</text>
  <defs>
    <marker id="ra" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
  </defs>
</svg>

**Con Métrica V3:**
- Requisitos de Usuario → documento de EVS (Estudio de Viabilidad)
- Requisitos del Sistema → Casos de Uso + Modelo de Dominio + UI

**Con Scrum:**
- Requisitos de Usuario → pliego de condiciones o lista de EPICs
- Requisitos del Sistema → **Product Backlog** (HUs + criterios de aceptación)

---

## Tipos de requisitos

| Tipo | Descripción | Ejemplos |
|------|-------------|---------|
| **Funcionales** | Lo que el sistema debe hacer | "El sistema permitirá inscribirse en una carrera" |
| **No funcionales** | Cómo debe ser (calidad) | Rendimiento, seguridad, usabilidad, portabilidad |
| **Integración** | Interfaces con otros sistemas | "Debe conectarse con la pasarela de pago X" |
| **Implantación** | Restricciones de despliegue | "Debe funcionar en Linux" |
| **Normativos** | Cumplimiento legal/normativo | GDPR, normativa tributaria |

---

## Características de un buen requisito

(ISO/IEC 29148)

| Característica | Descripción |
|----------------|-------------|
| **Necesario** | Sin él hay una deficiencia real, no es redundante |
| **Libre de implementación** | Dice el "qué", no el "cómo" |
| **Identificable** | Tiene un ID único (ej. R-001, 1.1.2) |
| **Singular** | Expresa una sola idea o propiedad |
| **Inequívoco** | Sin ambigüedades, interpretación única |
| **Trazable** | Se puede rastrear hacia arriba (origen) y hacia abajo (implementación/prueba) |
| **Verificable** | Se puede comprobar que el software lo satisface (mediante prueba o inspección) |

### Ejemplo — Mal requisito

> "Se pueden contratar algunos de los depósitos ofertados por la entidad bancaria mediante un ingreso de la cuenta asociada o por transferencia de otra entidad, abonándose los intereses en la cuenta asociada y en algunos casos en cuentas externas"

→ No es singular (mezcla varios hechos), no es inequívoco ("algunos", "en algunos casos").

### Ejemplo — Bien estructurado

1. Se pueden contratar los depósitos ofertados por la entidad bancaria.
2. El ingreso de fondos se puede realizar mediante transferencia de la cuenta asociada o por transferencia de otra entidad.
3. La liquidación de intereses se hace sobre la cuenta asociada y en algunos casos en cuentas externas.

→ Numerados jerárquicamente, singulares, identificables.

---

## Proceso de Ingeniería de Requisitos

<svg viewBox="0 0 500 90" width="500" height="90" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <rect x="10" y="20" width="95" height="32" rx="5" fill="#4a7fa5" opacity="0.5"/>
  <text x="57" y="40" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Elicitación</text>
  <line x1="105" y1="36" x2="128" y2="36" stroke="currentColor" stroke-width="1.5" marker-end="url(#pb)"/>
  <rect x="130" y="20" width="80" height="32" rx="5" fill="#4a7fa5" opacity="0.45"/>
  <text x="170" y="40" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Análisis</text>
  <line x1="210" y1="36" x2="233" y2="36" stroke="currentColor" stroke-width="1.5" marker-end="url(#pb)"/>
  <rect x="235" y="20" width="110" height="32" rx="5" fill="#4a7fa5" opacity="0.4"/>
  <text x="290" y="40" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Especificación</text>
  <line x1="345" y1="36" x2="368" y2="36" stroke="currentColor" stroke-width="1.5" marker-end="url(#pb)"/>
  <rect x="370" y="20" width="100" height="32" rx="5" fill="#5ba85e" opacity="0.5"/>
  <text x="420" y="40" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Validación</text>
  <path d="M 420,52 Q 420,72 57,72 Q 57,72 57,52" fill="none" stroke="currentColor" stroke-width="1.2" stroke-dasharray="5,3" marker-end="url(#pb)"/>
  <text x="238" y="82" font-size="8" fill="currentColor" text-anchor="middle" opacity="0.7">retroalimentación</text>
  <defs>
    <marker id="pb" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
  </defs>
</svg>

| Fase | Actividades |
|------|-------------|
| **Elicitación** | Entrevistas, talleres, observación. "¿Qué necesita el cliente?" |
| **Análisis** | Detectar inconsistencias, conflictos, lagunas |
| **Especificación** | Escribir el documento de requisitos (SRS) |
| **Validación** | Revisar con el cliente que los requisitos son correctos |

---

## Requisitos y contratación

**Pliego de Condiciones Técnicas:** documento en contratos públicos que especifica los requisitos del sistema a desarrollar. Contiene:
- Descripción de la situación actual
- Requisitos funcionales y no funcionales
- Requisitos de rendimiento y seguridad
- Entorno tecnológico
- Metodología de desarrollo exigida

Los requisitos del pliego son **los requisitos de usuario**. El contratista elabora a partir de ellos los **requisitos del sistema**.

---

## Organización con Story Mapping

Para Scrum, el Story Mapping organiza los requisitos en 2 dimensiones:
- **Eje X**: flujo del proceso del usuario (en orden temporal).
- **Eje Y**: prioridad (arriba = más importante).

Se agrupa por sprints → facilita la planificación de releases coherentes.

---

## Related Topics

- [[requisitos/Historias-de-Usuario]] — cómo elaborar las HUs a partir de los requisitos
- [[metodologia/Scrum-Agile]] — Product Backlog y Sprint Planning
- [[calidad/Pruebas-Software]] — verificación y validación de requisitos
