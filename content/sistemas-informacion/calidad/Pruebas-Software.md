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

<svg viewBox="0 0 500 80" width="500" height="80" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <rect x="10" y="20" width="90" height="36" rx="5" fill="#c0582a" opacity="0.5"/>
  <text x="55" y="35" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Error</text>
  <text x="55" y="49" font-size="9" fill="currentColor" text-anchor="middle">(humano)</text>
  <line x1="100" y1="38" x2="128" y2="38" stroke="currentColor" stroke-width="1.5" marker-end="url(#arr)"/>
  <rect x="130" y="20" width="110" height="36" rx="5" fill="#c0582a" opacity="0.4"/>
  <text x="185" y="35" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Defecto/Bug</text>
  <text x="185" y="49" font-size="9" fill="currentColor" text-anchor="middle">(en código)</text>
  <line x1="240" y1="38" x2="268" y2="38" stroke="currentColor" stroke-width="1.5" marker-end="url(#arr)"/>
  <rect x="270" y="20" width="100" height="36" rx="5" fill="#c0582a" opacity="0.6"/>
  <text x="320" y="35" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Fallo</text>
  <text x="320" y="49" font-size="9" fill="currentColor" text-anchor="middle">(en ejecución)</text>
  <line x1="370" y1="38" x2="398" y2="38" stroke="#5ba85e" stroke-width="1.5" marker-end="url(#arr2)"/>
  <rect x="400" y="20" width="90" height="36" rx="5" fill="#5ba85e" opacity="0.5"/>
  <text x="445" y="35" font-size="10" fill="currentColor" text-anchor="middle" font-weight="bold">Depuración</text>
  <text x="445" y="49" font-size="9" fill="currentColor" text-anchor="middle">→ corrección</text>
  <defs>
    <marker id="arr" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
    <marker id="arr2" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="#5ba85e"/>
    </marker>
  </defs>
</svg>

---

## Pruebas estáticas vs dinámicas

| Tipo | Descripción | Ejemplos |
|------|-------------|---------|
| **Estáticas** | Examinan el código sin ejecutarlo | Code review, Pull Requests, inspecciones |
| **Dinámicas** | Ejecutan el código con entradas concretas | Pruebas unitarias, integración, sistema |

Las **pruebas dinámicas** son el foco principal de la asignatura.

---

## Proceso de pruebas dinámicas

<svg viewBox="0 0 520 130" width="520" height="130" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <rect x="10" y="10" width="100" height="40" rx="5" fill="#4a7fa5" opacity="0.4"/>
  <text x="60" y="26" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">Test Basis</text>
  <text x="60" y="40" font-size="8" fill="currentColor" text-anchor="middle">(especificación)</text>
  <line x1="110" y1="30" x2="135" y2="30" stroke="currentColor" stroke-width="1.5" marker-end="url(#a1)"/>
  <rect x="137" y="10" width="120" height="40" rx="5" fill="#4a7fa5" opacity="0.4"/>
  <text x="197" y="26" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">Diseño e impl.</text>
  <text x="197" y="40" font-size="8" fill="currentColor" text-anchor="middle">casos de prueba</text>
  <line x1="257" y1="30" x2="282" y2="30" stroke="currentColor" stroke-width="1.5" marker-end="url(#a1)"/>
  <rect x="284" y="10" width="110" height="40" rx="5" fill="#4a7fa5" opacity="0.4"/>
  <text x="339" y="26" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">Casos de Prueba</text>
  <text x="339" y="40" font-size="8" fill="currentColor" text-anchor="middle">entradas + esperado</text>
  <line x1="339" y1="50" x2="339" y2="68" stroke="currentColor" stroke-width="1.5" marker-end="url(#a1)"/>
  <rect x="284" y="70" width="110" height="30" rx="5" fill="#c0582a" opacity="0.4"/>
  <text x="339" y="90" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">Ejecución</text>
  <line x1="284" y1="85" x2="259" y2="85" stroke="currentColor" stroke-width="1.5" marker-end="url(#a1)"/>
  <rect x="137" y="70" width="120" height="30" rx="5" fill="#c0582a" opacity="0.4"/>
  <text x="197" y="90" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">Comparación real vs esperado</text>
  <line x1="137" y1="85" x2="112" y2="85" stroke="currentColor" stroke-width="1.5" marker-end="url(#a1)"/>
  <rect x="10" y="70" width="100" height="30" rx="5" fill="#5ba85e" opacity="0.4"/>
  <text x="60" y="82" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">Reporting</text>
  <text x="60" y="95" font-size="8" fill="currentColor" text-anchor="middle">→ Defecto → Debug</text>
  <defs>
    <marker id="a1" markerWidth="6" markerHeight="6" refX="5" refY="3" orient="auto">
      <path d="M0,0 L6,3 L0,6 Z" fill="currentColor"/>
    </marker>
  </defs>
</svg>

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

<svg viewBox="0 0 520 180" width="520" height="180" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <rect x="10" y="10" width="140" height="32" rx="4" fill="#4a7fa5" opacity="0.5"/>
  <text x="80" y="30" font-size="10" fill="currentColor" text-anchor="middle">Requisitos de usuario</text>
  <rect x="30" y="55" width="120" height="32" rx="4" fill="#4a7fa5" opacity="0.4"/>
  <text x="90" y="75" font-size="10" fill="currentColor" text-anchor="middle">Requisitos sistema</text>
  <rect x="50" y="100" width="100" height="32" rx="4" fill="#4a7fa5" opacity="0.3"/>
  <text x="100" y="120" font-size="10" fill="currentColor" text-anchor="middle">Diseño técnico</text>
  <rect x="70" y="145" width="80" height="32" rx="4" fill="#4a7fa5" opacity="0.25"/>
  <text x="110" y="165" font-size="10" fill="currentColor" text-anchor="middle">Código fuente</text>
  <rect x="370" y="10" width="140" height="32" rx="4" fill="#c0582a" opacity="0.5"/>
  <text x="440" y="30" font-size="10" fill="currentColor" text-anchor="middle">Pruebas de Aceptación</text>
  <rect x="370" y="55" width="120" height="32" rx="4" fill="#c0582a" opacity="0.4"/>
  <text x="430" y="75" font-size="10" fill="currentColor" text-anchor="middle">Pruebas de Sistema</text>
  <rect x="370" y="100" width="100" height="32" rx="4" fill="#c0582a" opacity="0.35"/>
  <text x="420" y="120" font-size="10" fill="currentColor" text-anchor="middle">Pruebas Integración</text>
  <rect x="370" y="145" width="80" height="32" rx="4" fill="#c0582a" opacity="0.3"/>
  <text x="410" y="165" font-size="10" fill="currentColor" text-anchor="middle">Pruebas Unitarias</text>
  <line x1="150" y1="26" x2="180" y2="161" stroke="currentColor" stroke-width="1.2" stroke-dasharray="3,2"/>
  <line x1="150" y1="71" x2="175" y2="161" stroke="currentColor" stroke-width="1" stroke-dasharray="3,2" opacity="0.6"/>
  <line x1="150" y1="116" x2="172" y2="161" stroke="currentColor" stroke-width="1" stroke-dasharray="3,2" opacity="0.4"/>
  <line x1="370" y1="26" x2="340" y2="161" stroke="currentColor" stroke-width="1.2" stroke-dasharray="3,2"/>
  <line x1="370" y1="71" x2="345" y2="161" stroke="currentColor" stroke-width="1" stroke-dasharray="3,2" opacity="0.6"/>
  <line x1="370" y1="116" x2="348" y2="161" stroke="currentColor" stroke-width="1" stroke-dasharray="3,2" opacity="0.4"/>
  <text x="260" y="168" font-size="8" fill="currentColor" text-anchor="middle" opacity="0.6">↑ implementación</text>
  <line x1="150" y1="26" x2="370" y2="26" stroke="#5ba85e" stroke-width="1.5" stroke-dasharray="5,3"/>
  <line x1="150" y1="71" x2="370" y2="71" stroke="#5ba85e" stroke-width="1.2" stroke-dasharray="5,3" opacity="0.7"/>
  <line x1="150" y1="116" x2="370" y2="116" stroke="#5ba85e" stroke-width="1" stroke-dasharray="5,3" opacity="0.5"/>
  <line x1="150" y1="161" x2="370" y2="161" stroke="#5ba85e" stroke-width="1" stroke-dasharray="5,3" opacity="0.4"/>
</svg>

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
