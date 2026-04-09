---
title: Estándares ISO — Calidad y Pruebas
description: "ISO/IEC/IEEE 29119 (pruebas de software), ISO/IEC 29148 (ingeniería de requisitos). Terminología estándar, estructura y aplicación."
date: 2026-04-09
type: topic
subject: Sistemas de Información
sources: [si-iso29119.md, si-requisitos-software.md]
tags: [iso, estandares, calidad, pruebas, requisitos, sistemas-informacion, universidad]
---

# Estándares ISO — Calidad y Pruebas

## ISO/IEC/IEEE 29119 — Pruebas de Software

Estándar internacional que define el proceso, la documentación y las técnicas de pruebas de software. Reemplaza múltiples normas anteriores (BS 7925-1, IEEE 829, etc.).

### Estructura (5 partes)

| Parte | Título | Contenido |
|-------|--------|-----------|
| 29119-1 | Concepts and Definitions | Terminología estándar |
| 29119-2 | Test Processes | Proceso de pruebas (planificación, gestión, ejecución) |
| 29119-3 | Test Documentation | Plantillas de documentos (plan, caso, informe) |
| 29119-4 | Test Techniques | Técnicas de diseño de pruebas |
| 29119-5 | Keyword-Driven Testing | Pruebas basadas en palabras clave |

### Terminología clave (29119-1)

| Término estándar | Equivalente coloquial |
|------------------|-----------------------|
| **Test Condition** | Condición de entrada (aspecto a probar) |
| **Test Coverage Item** | Clase de equivalencia (situación concreta a cubrir) |
| **Test Case** | Caso de prueba |
| **Test Suite** | Conjunto de casos de prueba |
| **Test Basis** | Base de pruebas (especificación de la que se deriva) |

### Proceso de pruebas (29119-2)

```
Planificación → Diseño e implementación → Ejecución → Reporting → Cierre
```

- **Planificación**: estrategia, alcance, recursos, riesgos.
- **Diseño**: derivar los casos de prueba a partir de la Test Basis.
- **Ejecución**: ejecutar los casos, registrar resultados.
- **Reporting**: informe de pruebas, defectos encontrados.
- **Cierre**: análisis de cobertura, lecciones aprendidas.

---

## ISO/IEC/IEEE 29148 — Ingeniería de Requisitos

Estándar para el ciclo de vida de los requisitos:

- Define el proceso de **stakeholder requirements definition** (requisitos de usuario).
- Define el proceso de **requirements analysis** (requisitos del sistema).
- Establece las **características** que deben tener los requisitos (necesario, libre de implementación, singular, inequívoco, trazable, verificable — ver [[requisitos/Requisitos-Software]]).

---

## Gestión de Configuración del Software (SCM)

La gestión de configuración controla los cambios en los artefactos del proyecto:

| Actividad | Descripción |
|-----------|-------------|
| **Control de versiones** | Registro de todos los cambios (Git) |
| **Control de cambios** | Proceso formal para aprobar modificaciones |
| **Auditoría de configuración** | Verificar que lo que se entrega coincide con lo aprobado |
| **Identificación** | Naming conventions, tags, releases |

En el proyecto de SI: Git + GitHub + Pull Requests como herramienta de SCM y revisión.

---

## ISO 9001 — Gestión de Calidad

Marco general de gestión de calidad (no específico de software):
- Orientado a **procesos** y **mejora continua**.
- Requiere documentación de procesos y seguimiento de métricas.
- Las empresas de software pueden certificarse en ISO 9001.

---

## CMMI — Capability Maturity Model Integration

Modelo de madurez para procesos de desarrollo software:

| Nivel | Nombre | Características |
|-------|--------|-----------------|
| 1 | Inicial | Procesos ad-hoc, impredecibles |
| 2 | Gestionado | Proyectos planificados y controlados |
| 3 | Definido | Procesos estándar en toda la organización |
| 4 | Cuantitativamente gestionado | Métricas y control estadístico |
| 5 | En optimización | Mejora continua cuantitativa |

---

## Related Topics

- [[calidad/Pruebas-Software]] — técnicas de prueba (29119-4)
- [[requisitos/Requisitos-Software]] — características de requisitos (29148)
- [[practicas/Git-y-Pull-Requests]] — SCM en la práctica
