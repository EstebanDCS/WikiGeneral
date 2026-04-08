---
title: Overfitting y Underfitting
description: Cómo detectar sobreajuste y subajuste con curvas de aprendizaje y cómo corregirlos.
date: 2026-04-08
type: topic
subject: Sistemas Inteligentes
sources: [2.1-regresion.md, 2.2-validacion.md, 2.3-clasificacion.md, 3.1-redes-neuronales.md]
tags: [overfitting, underfitting, generalizacion, sistemas-inteligentes, universidad]
---

## Definición

El equilibrio entre underfitting y overfitting es el problema central del aprendizaje automático: encontrar un modelo que aprenda lo suficiente de los datos de entrenamiento para generalizar bien a datos nuevos.

---

## Underfitting (Infraajuste)

El modelo **no es capaz de aprender** ni siquiera los patrones del conjunto de entrenamiento.

- Error alto en train **y** en test
- El modelo es demasiado simple para el problema

**Causas:**
- Modelo demasiado simple (ej: regresión lineal para datos cuadráticos)
- Pocas iteraciones de entrenamiento
- Learning rate muy pequeño

**Soluciones:**
- Aumentar la complejidad del modelo (más capas/neuronas, mayor grado polinómico)
- Entrenar más épocas
- Usar un modelo diferente

---

## Overfitting (Sobreajuste)

El modelo **memoriza los datos de entrenamiento** pero falla con datos nuevos.

- Error bajo en train, error alto en test
- El modelo es demasiado complejo para la cantidad de datos disponibles

**Causas:**
- Modelo demasiado complejo
- Pocos datos de entrenamiento
- Ruido en los datos que el modelo aprende como si fuera señal

**Ejemplo visual con regresión polinómica:**

| Grado | Train R² | Test R² | Estado |
|-------|---------|---------|--------|
| 1 | 0.64 | 0.62 | Underfitting |
| 3 | 0.95 | 0.93 | Equilibrado |
| 30 | 0.99 | 0.21 | Overfitting |

---

## Cómo detectarlo: curvas de aprendizaje

Representar el error de train y de validación a lo largo del entrenamiento:

```
Error
│
│ ╲  train
│  ╲_______________
│           val ╲___/
│                   ← aquí empieza el overfitting
└─────────────────── Épocas
```

Cuando la curva de validación empieza a subir mientras la de train sigue bajando → overfitting.

---

## Soluciones por tipo de modelo

| Modelo | Soluciones overfitting |
|--------|----------------------|
| Regresión polinómica | Reducir grado, AIC/BIC |
| Árbol de decisión | Pre-poda o post-poda |
| SVR/SVM | Reducir C |
| KNN | Aumentar K |
| Red neuronal | Dropout, Data Augmentation, Early Stopping |

---

## La regla de oro: validación

**Siempre evaluar en datos que el modelo no ha visto.** Ver [[Validación de Modelos]] para las técnicas correctas (Hold-out, K-fold, Meta-validación).

## Fuentes

- [[2.1-regresion]] · [[2.2-validacion]] · [[2.3-clasificacion]] · [[3.1-redes-neuronales]]

## Related Topics

- [[Validación de Modelos]]
- [[Regresión]]
- [[Clasificación]]
- [[Redes Neuronales]]
