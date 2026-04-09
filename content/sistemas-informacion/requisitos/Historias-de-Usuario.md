---
title: Historias de Usuario y Sprint Backlog
description: "Historias de Usuario (HU): formato, criterios de aceptación, refinamiento, priorización. Elaboración del Product Backlog y Sprint Backlog."
date: 2026-04-09
type: topic
subject: Sistemas de Información
sources: [si-requisitos-software-hu.md]
tags: [historias-usuario, sprint-backlog, criterios-aceptacion, scrum, sistemas-informacion, universidad]
---

# Historias de Usuario y Sprint Backlog

## Contexto

En Scrum, los **requisitos del sistema** se expresan como **Historias de Usuario (HU)**, que conforman el **Product Backlog** y luego el **Sprint Backlog** de cada iteración.

---

## Historia de Usuario

### Formato estándar

```
Como [tipo de usuario],
quiero [objetivo/capacidad],
para que [beneficio/razón].
```

**Ejemplo:**
> Como atleta, quiero inscribirme en una carrera popular, para que pueda participar en la prueba.

### Las 3 C de las HU (Ron Jeffries)

| C | Descripción |
|---|-------------|
| **Card** | La HU como tarjeta física/digital (breve, concisa) |
| **Conversation** | Diálogo continuo con el Product Owner para clarificar |
| **Confirmation** | Criterios de aceptación que confirman que está "hecha" |

---

## Criterios de Aceptación

Condiciones que debe cumplir la HU para considerarse completada. Se formulan como escenarios:

```
Dado [contexto inicial],
cuando [acción del usuario],
entonces [resultado esperado].
```

**Ejemplo para la HU de inscripción:**

| # | Dado | Cuando | Entonces |
|---|------|--------|----------|
| CA1 | Atleta mayor de edad, modalidad popular, inscripción en plazo | Completa el formulario | Se registra la inscripción con descuento del 30% |
| CA2 | Atleta menor de edad | Intenta inscribirse | El sistema rechaza la solicitud |
| CA3 | Atleta extranjero, modalidad élite | Intenta inscribirse | El sistema rechaza la solicitud |
| CA4 | Atleta, inscripción el día de la prueba | Completa el formulario | Se registra con recargo del 50% |

Los criterios de aceptación son la **base de las pruebas** de la HU.

---

## Definition of Done (DoD)

Descripción formal del estado que debe tener un incremento para considerarse completo (Scrum Guide 2020):

> "Una descripción formal del estado del incremento cuando cumple con las medidas de calidad requeridas para el producto."

**DoD típica en SI:**
- ✅ Código integrado en el repositorio (rama correspondiente)
- ✅ Revisado mediante Pull Request (revisión estática)
- ✅ Pruebas automatizadas implementadas y pasando (pruebas dinámicas)
- ✅ Funcionalidad demostrable en el entorno de integración

---

## Refinamiento del Backlog

El Product Backlog evoluciona continuamente:

1. **Creación**: HUs iniciales a partir de los requisitos de usuario (pliego).
2. **Refinamiento**: aclarar, dividir, estimar (story points) y priorizar.
3. **Priorización** (MoSCoW): Must, Should, Could, Won't (para este sprint).
4. **Sprint Planning**: seleccionar las HUs del backlog para el sprint actual.

**Estimación con Story Points**: puntuación relativa de complejidad (escala Fibonacci: 1, 2, 3, 5, 8, 13...). El equipo estima por consenso (Planning Poker).

---

## Del Requisito de Usuario a la HU

**Paso 1**: Analizar el pliego/oferta → identificar objetivos y partes interesadas.

**Paso 2**: Crear EPICs (funcionalidades grandes) → dividirlas en HUs.

**Paso 3**: Para cada HU, escribir los criterios de aceptación.

**Paso 4**: Crear el Sprint Backlog (HUs con mayor prioridad para el primer sprint).

**Paso 5**: Añadir prototipos de UI y modelo de datos si es necesario.

### Caso práctico — Carreras populares

Objetivos del sistema:
1. Facilitar la inscripción a competiciones.
2. Mantener actualizado el registro de inscripciones.
3. Registrar tiempos y elaborar clasificaciones.

EPICs derivadas:
- Gestión de inscripciones
- Gestión de pagos
- Gestión de resultados/clasificaciones

HUs del EPIC "Gestión de inscripciones":
- Como atleta, quiero inscribirme en una carrera, para participar.
- Como club, quiero inscribir a varios atletas en lote, para ahorrar tiempo.
- Como organizador, quiero ver la lista de inscritos, para gestionar la prueba.

---

## Prototipos

Se incluyen **mockups** de las pantallas asociadas a cada HU. Sirven para:
- Validar el entendimiento con el Product Owner.
- Guiar el diseño de la interfaz de usuario.
- Clarificar los criterios de aceptación.

---

## Modelo de Datos

Junto a las HUs se elabora un **modelo de dominio** (clases con atributos y relaciones) que representa las entidades del sistema:
- Carrera, Atleta, Club, Inscripción, Pago, Resultado...

---

## Related Topics

- [[requisitos/Requisitos-Software]] — del pliego a los requisitos del sistema
- [[metodologia/Scrum-Agile]] — Product Backlog, Sprint Backlog, Planning
- [[calidad/Pruebas-Software]] — los criterios de aceptación → casos de prueba
- [[practicas/Git-y-Pull-Requests]] — gestión del código por HU en ramas Git
