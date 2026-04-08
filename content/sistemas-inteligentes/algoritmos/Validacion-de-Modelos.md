---
title: Validación de Modelos
description: "Hold-out, K-fold y meta-validación. Regla de oro: nunca normalizar con datos de test."
date: 2026-04-08
type: topic
subject: Sistemas Inteligentes
sources: [2.2-validacion.md]
tags: [validacion, cross-validation, hold-out, overfitting, sistemas-inteligentes, universidad]
---

## Definición

La validación es el proceso de comprobar que un modelo **funciona bien con datos nuevos**, no solo con los que usó para entrenar.

**El problema fundamental:** un modelo puede memorizar los datos de entrenamiento (overfitting) y fallar con datos nuevos. Hay que simular ese escenario de forma controlada.

> Queremos modelos que **ajusten los datos**, pero con **capacidad de generalización**.

---

## Por qué falla la Resustitución

**Resustitución** = entrenar y evaluar con los mismos datos.

```
DATA → [Modelo] → Evaluar en DATA → R²=0.95  ← ¡MENTIRA!
```

El modelo ha "visto" los datos durante el entrenamiento, así que los predice perfectamente. Esta evaluación es **inaceptablemente optimista** y no dice nada sobre el rendimiento real.

**Analogía:** estudiar exactamente las preguntas del examen que ya tienes, y luego sacar un 10. No demuestra que hayas aprendido.

---

## Hold-Out

Dividir el dataset en dos partes **antes** de entrenar nada:

```
DATA (100%)
├── TRAIN (80%)  ← el modelo solo ve esto
└── TEST  (20%)  ← se reserva hasta el final
```

**Proceso:**
1. Separar train y test
2. Entrenar el modelo **solo con train**
3. Evaluar **solo con test** (una sola vez)

**Ejemplo de resultados:**

| Modelo | R² (Train) | R² (Test) |
|--------|-----------|-----------|
| SVR    | 0.75      | 0.80      |
| KNN    | 0.72      | 0.82      |
| Árbol  | 0.95      | 0.70      | ← overfitting claro |

El árbol "memoriza" train (R²=0.95) pero generaliza mal (R²=0.70).

**Cuándo usarlo:** datasets grandes (la partición del 20% tiene suficientes ejemplos para estimar bien el error).

**Limitación:** con pocos datos, el 20% de test puede no ser representativo.

---

## Validación Cruzada (K-Fold Cross-Validation)

Dividir el dataset en **K grupos** de igual tamaño. Hacer K entrenamientos: en cada uno, K-1 grupos son train y 1 es test. El resultado final es la **media de las K evaluaciones**.

**Ejemplo con K=4:**

```
DATA → [G1][G2][G3][G4]

IT1: TEST=[G1], TRAIN=[G2,G3,G4]
IT2: TEST=[G2], TRAIN=[G1,G3,G4]
IT3: TEST=[G3], TRAIN=[G1,G2,G4]
IT4: TEST=[G4], TRAIN=[G1,G2,G3]
```

**Resultados (K=4):**

| Modelo | IT1  | IT2  | IT3  | IT4  | Media |
|--------|------|------|------|------|-------|
| SVR    | 0.80 | 0.81 | 0.75 | 0.83 | **0.80** |
| KNN    | 0.82 | 0.80 | 0.70 | 0.65 | **0.74** |
| Árbol  | 0.70 | 0.60 | 0.82 | 0.80 | **0.73** |

**Cuándo usarlo:**
- Datasets grandes: K pequeño (normalmente K=10)
- Datasets pequeños: K grande (hasta Leave-One-Out, donde K=n)

**Ventaja:** todos los datos sirven tanto para train como para test (en diferentes iteraciones).

---

## Meta-Validación (Train / Dev / Test)

Cuando el modelo tiene **hiperparámetros** que ajustar (ej: grado del polinomio, C de SVR, K de KNN), necesitamos un tercer conjunto para no "contaminar" el test.

```
DATA (100%)
├── TRAIN (~80%)  ← para entrenar
├── DEV   (~10%)  ← para ajustar hiperparámetros
└── TEST  (~10%)  ← evaluación final, solo al final
```

**Proceso:**
1. Para cada combinación de hiperparámetros → entrenar en TRAIN, evaluar en DEV
2. Escoger la mejor combinación según DEV
3. Entrenar el modelo final en TRAIN+DEV con esa combinación
4. Evaluar **una sola vez** en TEST → este es el resultado real

**Por qué no usar TEST para elegir hiperparámetros:**
Si usas TEST para tomar decisiones, estás indirectamente ajustando el modelo a TEST, y deja de ser una estimación honesta del rendimiento futuro.

---

## Regla de Oro: Normalización con Validación

Cuando se normaliza/estandariza, los parámetros (media, desviación, mín, máx) se deben calcular **solo con TRAIN** y luego aplicar ese mismo escalado a TEST.

**Correcto:** calcular $\mu$ y $\sigma$ **solo con TRAIN**, aplicar ese mismo escalado a TEST:

> TRAIN → calcular $\mu_\text{train}$, $\sigma_\text{train}$ → normalizar TRAIN  
> TEST → normalizar con $\mu_\text{train}$, $\sigma_\text{train}$ ← **mismos parámetros**

**Incorrecto (Data Leakage):** calcular $\mu$ y $\sigma$ propios de TEST para normalizarlo.

**Por qué:** en producción no conocemos los datos futuros. Si normalizamos TEST con sus propios parámetros, estamos usando información del futuro que en la práctica no tendríamos.

---

## Resumen comparativo

| Técnica | Splits | Cuándo usar | Limitación |
|---------|--------|------------|------------|
| Resustitución | 0 | Nunca | Completamente optimista |
| Hold-Out | Train/Test | Datasets grandes | Sesgo si pocos datos |
| K-Fold CV | K rondas | Siempre que sea posible | Más costoso computacionalmente |
| Meta-validación | Train/Dev/Test | Con hiperparámetros | Requiere más datos |

## Fuentes

- [[2.2-validacion]]

## Related Topics

- [[Regresión]]
- [[Clasificación]]
- [[Redes Neuronales]]
- [[Overfitting y Underfitting]]
- [[Preprocesado de Datos]]
