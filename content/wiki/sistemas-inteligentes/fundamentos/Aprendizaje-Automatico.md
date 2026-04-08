---
title: Aprendizaje Automático
description: Tipos de aprendizaje: supervisado, no supervisado y por refuerzo. Cuándo usar cada enfoque.
date: 2026-04-08
type: topic
subject: Sistemas Inteligentes
sources: [1.0-introduccion.md]
tags: [aprendizaje-automatico, machine-learning, clasificacion, regresion, clustering, sistemas-inteligentes, universidad]
---

## Definición

> "Estudio de algoritmos informáticos que mejoran automáticamente con la experiencia sin ser programados."

A diferencia de la programación clásica, en AA **no se escriben reglas**: el sistema aprende esas reglas a partir de los datos.

## Programación clásica vs Aprendizaje Automático

| Aspecto | Programación clásica | Aprendizaje Automático |
|---------|---------------------|----------------------|
| Quién define las reglas | El programador | El algoritmo (a partir de datos) |
| Comportamiento | Estático | Se adapta a los datos |
| Generalización | Solo lo que se programó | Generaliza desde ejemplos |
| Esfuerzo principal | Diseñar la lógica | Preparar datos y elegir modelos |

### ¿Cuándo usar AA?

**No hace falta AA** → cuando la solución se puede programar explícitamente:
- Calcular el área de un rectángulo (base × altura) ✓

**Hace falta AA** → cuando no existe una fórmula o regla clara:
- Predecir el precio de una casa dada su superficie, antigüedad y habitaciones ✗ (demasiadas variables interrelacionadas)
- Extraer los dígitos de una matrícula ✗ (imposible enumerar todas las variantes de fuentes/ángulos/iluminación)

---

## Taxonomía completa

```
Aprendizaje Automático
├── Supervisado
│   ├── Clasificación
│   │   ├── Binaria        (ej: aprobado/suspendido)
│   │   ├── Multi-clase    (ej: grupo A/B/C)
│   │   └── Multi-etiqueta (ej: varias etiquetas simultáneas)
│   └── Regresión
│       ├── Lineal
│       └── Polinómica
├── No supervisado
│   └── Clustering         (agrupar sin etiquetas)
└── Por refuerzo            (aprender por prueba y error)
```

---

## Aprendizaje Supervisado

El modelo aprende a predecir una salida **y** a partir de entradas **x**, usando ejemplos etiquetados (pares x→y conocidos).

### Clasificación

Predice una **categoría** (clase discreta).

#### Clasificación Binaria
Solo dos posibles salidas.
- Ejemplo: ¿Este alumno aprobará? → `Sí / No`
- La salida se codifica como `0` o `1`

#### Clasificación Multi-clase
Más de dos clases posibles, **mutuamente excluyentes** (solo una clase por ejemplo).
- Ejemplo: ¿A qué grupo pertenece el alumno? → `PL-1 / PL-2 / PL-3`
- Codificación: **One-Hot Encoding** (ver [[Codificación de Datos]])

| Alumno | Teoría | Prácticas | Grupo → PL-1 | PL-2 | PL-3 |
|--------|--------|-----------|-------------|------|------|
| A001   | 0.60   | -0.92     | 1           | 0    | 0    |
| A002   | 1.00   | -0.30     | 0           | 0    | 1    |

#### Clasificación Multi-etiqueta
Un ejemplo puede pertenecer a **varias clases simultáneamente**.
- Ejemplo: Un alumno puede estar en PL-1 **y** PL-2 a la vez
- Codificación: One-Hot con un `1` por cada clase activa

### Regresión

Predice un **valor numérico continuo**.
- Ejemplo: ¿Cuánto costará esta casa? → `187.500 €`
- Ejemplo: ¿Qué nota sacar en el examen? → `7.4`

---

## Aprendizaje No Supervisado

No hay etiquetas. El modelo descubre **estructura oculta** en los datos por sí solo.

### Clustering

Agrupa los datos en **clústeres** (grupos) según su similitud, sin saber de antemano cuántos grupos hay ni a cuál pertenece cada ejemplo.

- Ejemplo: Agrupar clientes por comportamiento de compra
- Algoritmos típicos: K-Means, DBSCAN, Clustering jerárquico

---

## Aprendizaje por Refuerzo

Un **agente** aprende a tomar decisiones en un **entorno** mediante **prueba y error**. No hay etiquetas: el agente recibe recompensas o penalizaciones según sus acciones.

### Ejemplo ilustrativo: Robot en un tablero

- **Agente**: Robot
- **Objetivo**: Llegar al cofre lo más rápido posible
- **Entorno**: Tablero con montañas y monstruos
- **Sistema de puntuación**:
  - Cada movimiento: +1 punto de coste
  - Pasar por montaña: +5 puntos (es difícil)
  - Tocar monstruo: +15 puntos (reinicia el recorrido)
- El agente prueba rutas distintas y aprende cuál minimiza el coste total

---

## Fuentes

- [[1.0-introduccion]]

## Related Topics

- [[Inteligencia Artificial]]
- [[Proceso KDD]]
- [[Preprocesado de Datos]]
- [[Codificación de Datos]]
- [[Aprendizaje por Refuerzo]]
