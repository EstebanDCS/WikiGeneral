---
title: Pruebas del Software
description: "Pruebas del software: definiciones (error/defecto/fallo), técnicas (caja negra/blanca), clases de equivalencia, modelo en V, automatización con JUnit."
date: 2026-04-09
type: topic
subject: Sistemas de Información
sources: [si-pruebas-parte1.md]
tags: [pruebas, testing, clases-equivalencia, junit, caja-negra, sistemas-informacion, universidad]
---

# Pruebas del Software

## Por qué son necesarias las pruebas

Ejemplos reales de fallos:

- **AEAT (2013)**: error en módulo general de errores del sistema tributario.
- **PayPal (2013)**: cliente recibió un balance de $92 cuatrillones por email.
- **AWS (oct. 2025)**: race condition en automatización DNS → caída de DynamoDB en us-east-1 → múltiples servicios afectados globalmente.
- **Shopify (2022-2025)**: cupones de descuento no aplicaban en combinación con envío internacional → tasa de abandono del carrito del 70%.

> Los fallos no aparecen por azar: aparecen donde no se ha probado.

---

## Definiciones clave

| Término | Definición |
|---------|-----------|
| **Error** | Equivocación cometida por un humano (programador, analista) |
| **Defecto (Bug, Fault)** | "Desperfecto" en el código que puede causar un fallo |
| **Fallo (Failure)** | Diferencia entre el comportamiento esperado y el observado en ejecución |

```
Error (humano) → Defecto (en código) → Fallo (en ejecución)
                                           ↓
                                       Depuración → Corrección del defecto
```

---

## Pruebas estáticas vs dinámicas

| Tipo | Descripción | Ejemplos |
|------|-------------|---------|
| **Estáticas** | Examinan el código sin ejecutarlo | Code review, Pull Requests, inspecciones |
| **Dinámicas** | Ejecutan el código con entradas concretas | Pruebas unitarias, integración, sistema |

Las **pruebas dinámicas** son el foco principal de la asignatura.

---

## Proceso de pruebas dinámicas

```
Test Basis          →  Diseño e implementación  →  Casos de Prueba
(especificación)        de casos de prueba          (entradas + salida esperada)
                                                        ↓
                                                   Ejecución
                                                        ↓
                                              Comparación (real vs esperada)
                                                        ↓
                                              Reporting → Defecto → Depuración
```

**Caso de prueba (CP):** conjunto de {objetivo, entradas, condiciones de ejecución, salida esperada}.

**Test Suite:** colección de casos de prueba para una funcionalidad.

> Un caso de uso (o HU) genera **múltiples** casos de prueba para diferentes situaciones.

---

## Enfoques

| Enfoque | Qué usa como base | Aka |
|---------|------------------|-----|
| **Caja blanca (White Box)** | Código fuente | Pruebas estructurales |
| **Caja negra (Black Box)** | Especificación/requisitos | Pruebas funcionales |

En ISOF (asignatura anterior): se usó caja blanca (cobertura de condiciones/decisiones).
En SI: se usa **caja negra** con la técnica de **Clases de Equivalencia**, partiendo de los requisitos y criterios de aceptación de las HUs.

---

## Técnica: Clases de Equivalencia (Partición de Equivalencia)

Divide los dominios de entrada en **clases de equivalencia**: conjuntos de valores para los que el programa tiene comportamiento similar. Solo se necesita probar un valor representativo de cada clase.

### Pasos

1. Identificar **condiciones de entrada** (aspectos del sistema a probar).
2. Para cada condición, definir **clases de equivalencia** (situaciones concretas).
   - Clases **válidas** (comportamiento esperado correcto).
   - Clases **inválidas** (entradas que el sistema debe rechazar).
3. Derivar **casos de prueba**:
   - Cubrir todas las clases válidas con el mínimo de casos.
   - Un caso por cada clase inválida (para evitar enmascaramiento).

### Ejemplo: inscripción en carrera popular

**Función:** inscribirse en una carrera (mayor de edad, modalidad popular/élite, con descuento/recargo según fecha).

| Condición de entrada | Clases válidas | Clases inválidas |
|---------------------|----------------|-----------------|
| Edad del solicitante | ≥ 18 años | < 18 años |
| Modalidad | Popular / Élite | Cualquier otra |
| Nacionalidad | Española / Extranjera | — |
| Fecha de inscripción | Plazo I (antes tope, -30%) · Plazo II (entre tope y prueba) · Plazo III (día de la prueba, +50%) | Antes de apertura · Después de la prueba |
| Estado inscripción | No inscrito | Ya inscrito previamente |

**Casos de prueba derivados:**

| CP | Edad | Modalidad | Nacion. | Fecha | Estado | Resultado esperado |
|----|------|-----------|---------|-------|--------|-------------------|
| 1 | ≥18 | Popular | Esp. | Plazo I | No inscrito | OK, -30% |
| 2 | ≥18 | Popular | Esp. | Plazo II | No inscrito | OK, precio base |
| 3 | ≥18 | Popular | Esp. | Plazo III | No inscrito | OK, +50% |
| 4 | ≥18 | Élite | Esp. | Plazo I | No inscrito | OK, -30% |
| 5 | <18 | Popular | Esp. | Plazo I | No inscrito | **KO** — menor de edad |
| 6 | ≥18 | Élite | Extr. | Plazo I | No inscrito | **KO** — extranjero en élite |
| 7 | ≥18 | Popular | Esp. | Antes apertura | No inscrito | **KO** — fuera de plazo |
| 8 | ≥18 | Popular | Esp. | Plazo I | Ya inscrito | **KO** — ya inscrito |

---

## Modelo en V — Niveles y tipos de prueba

```
Requisitos de usuario  ←──────────────────────► Pruebas de Aceptación
    Requisitos sistema  ←──────────────────────► Pruebas de Sistema
        Diseño técnico  ←──────────────────────► Pruebas de Integración
           Código fuente ←──────────────────────► Pruebas Unitarias
```

| Nivel | Objetivo | Responsable |
|-------|----------|-------------|
| **Unitarias** | Probar una función/clase aislada | Programador |
| **Integración** | Probar componentes juntos | Programador/equipo |
| **Sistema** | Probar el sistema completo | QA |
| **Aceptación** | Validar con el cliente | Cliente/QA |

---

## Automatización con JUnit

En el proyecto de la asignatura, las pruebas se automatizan con **JUnit** (Java):

```java
@Test
void testInscripcionMenorDeEdad() {
    // Dado: solicitante menor de edad
    Solicitante s = new Solicitante(15, Modalidad.POPULAR, "ES");
    // Cuando: intenta inscribirse
    assertThrows(InscripcionException.class, () -> {
        sistema.inscribir(s, carrera, LocalDate.now());
    });
}

@Test
void testDescuento30PorcientoAntesDeadline() {
    // Dado: atleta mayor de edad en plazo I
    Solicitante s = new Solicitante(25, Modalidad.POPULAR, "ES");
    LocalDate fechaEnPlazoI = carrera.getFechaDeadline().minusDays(5);
    // Cuando: se inscribe
    Inscripcion ins = sistema.inscribir(s, carrera, fechaEnPlazoI);
    // Entonces: precio con 30% de descuento
    assertEquals(carrera.getPrecioBase() * 0.70, ins.getPrecio(), 0.01);
}
```

---

## Related Topics

- [[calidad/Estandares-ISO]] — ISO 29119, marco estándar de pruebas
- [[requisitos/Historias-de-Usuario]] — criterios de aceptación → base de los CPs
- [[practicas/Git-y-Pull-Requests]] — Pull Requests como revisión estática (prueba estática)
