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

```
Negocio     →  Requisitos de Negocio  (objetivos de la organización)
     ↓
Usuario     →  Requisitos de Usuario  (qué necesitan los stakeholders)
     ↓
Sistema     →  Requisitos del Sistema (qué debe hacer el producto exactamente)
```

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

```
Elicitación → Análisis → Especificación → Validación
     ↑___________________________|
```

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
