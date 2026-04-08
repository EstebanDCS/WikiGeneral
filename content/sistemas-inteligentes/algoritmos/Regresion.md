---
title: Regresión
description: "Métodos de regresión: lineal, polinómica, SVR, árboles, KNN y Bayesiano. Métricas R², MAE, RMSE."
date: 2026-04-08
type: topic
subject: Sistemas Inteligentes
sources: [2.1-regresion.md]
tags: [regresion, machine-learning, sistemas-inteligentes, universidad]
---

## Definición

La regresión predice una **variable numérica continua** (Y) a partir de una o varias variables de entrada (X).

- Ejemplos: predecir el precio de una casa, la altura de una persona, la temperatura de mañana.
- Es un tipo de [[Aprendizaje Automático]] **supervisado**.

---

## Métricas de evaluación

Todas las métricas comparan el valor real **y** con el predicho **ŷ**:

| Métrica | Fórmula | Qué mide | Rango |
|---------|---------|---------|-------|
| **R²** (coef. determinación) | 1 - SS_res/SS_tot | Proporción de varianza explicada | [0, 1] → 1 es perfecto |
| **MAE** (error absoluto medio) | (1/n) Σ\|y - ŷ\| | Error medio en las mismas unidades que Y | 0 = perfecto |
| **RMSE** (raíz error cuadrático) | √((1/n) Σ(y - ŷ)²) | Como MAE pero penaliza más los errores grandes | 0 = perfecto |

---

## Métodos

### Baselines — punto de referencia mínimo

Un baseline es el modelo más simple posible. Si tu modelo no lo supera, algo va mal.

| Baseline | Predicción | Cuándo usarlo |
|---------|-----------|--------------|
| **Baseline media** | Siempre retorna la media de Y en train | Siempre como referencia |
| **Baseline mediana** | Siempre retorna la mediana de Y en train | Cuando hay muchos outliers |

---

### Regresión Lineal

Predice Y como combinación lineal de las X.

**Simple** (1 variable): `ŷ = β₁·x + β₀`

**Multivariante** (n variables): `ŷ = β₀ + β₁·x₁ + β₂·x₂ + ... + βₙ·xₙ`

Los coeficientes β se calculan por **mínimos cuadrados** (minimizar la suma de (y - ŷ)²).

**Interpretación de los coeficientes:** β₁ = cambio esperado en Y por cada unidad que aumenta x₁.

| Ventajas | Desventajas |
|----------|-------------|
| Computacionalmente muy eficiente | Solo modela relaciones lineales |
| Fácil de interpretar | Sensible a outliers |
| Funciona bien con datasets grandes | No captura relaciones complejas |

---

### Regresión Polinómica

Extiende la lineal añadiendo potencias de X: `ŷ = β₀ + β₁·x + β₂·x² + ... + βₙ·xⁿ`

**El problema: ¿qué grado n elegir?**

| Grado | R² (ejemplo) | RMSE | Problema |
|-------|-------------|------|---------|
| 1 (lineal) | 0.639 | 597 | Underfitting |
| 2 | 0.769 | 382 | — |
| 3 | 0.951 | 81 | — |
| 30 | 0.949 | 48 | Overfitting (más parámetros, no mejor R²) |

**Criterios para elegir n:**
- **AIC** (Akaike Information Criterion): penaliza complejidad
- **BIC** (Bayesian Information Criterion): penaliza complejidad más fuerte que AIC

| Ventajas | Desventajas |
|----------|-------------|
| Modela relaciones no lineales | Riesgo alto de overfitting |
| Simple de calcular | Predicciones malas fuera del rango de entrenamiento |

---

### SVR — Support Vector Regression

Adapta la SVM a regresión. El modelo acepta error de hasta ε (margen) sin penalizar; los puntos fuera del margen sí tienen coste.

**Hiperparámetros clave:**
- **C**: controla la penalización por salirse del margen. C alto → más overfitting; C bajo → más libertad
- **ε**: anchura del margen de tolerancia
- **kernel**: tipo de transformación para datos no lineales

**Kernels:**

| Kernel | Fórmula | Cuándo |
|--------|---------|--------|
| Lineal | K(x,z) = ⟨x,z⟩ | Datos linealmente separables |
| Polinómico | K(x,z) = (⟨x,z⟩ + c)^d | Relaciones polinómicas |
| RBF (Gaussian) | K(x,z) = exp(-γ‖x-z‖²) | Caso general, muy flexible |

| Ventajas | Desventajas |
|----------|-------------|
| Robusto frente a outliers | Lento en datasets grandes |
| Flexible con kernels | Difícil elegir kernel e hiperparámetros |
| Efectivo en alta dimensión | Requiere normalización previa |

---

### Árboles de Regresión

Dividen el espacio de entrada en regiones rectangulares y predicen la **media de Y** en cada región.

**Funcionamiento:**
1. Buscar el corte que minimiza la varianza dentro de cada región
2. Repetir recursivamente hasta criterio de parada
3. Predicción = media de los valores que caen en esa región

**Variante**: árboles que aprenden un modelo lineal en cada hoja (útil si los datos son lineales en cada región).

| Ventajas | Desventajas |
|----------|-------------|
| Visualizable e interpretable | Propenso a overfitting |
| No requiere normalización | Sensible a variaciones pequeñas |
| Maneja relaciones lineales y no lineales | Mal fuera del rango de entrenamiento |

---

### KNN Regresión — K-Vecinos Más Próximos

No requiere entrenamiento. Para predecir un nuevo punto:
1. Calcular la distancia a todos los puntos de train
2. Seleccionar los K más cercanos
3. Retornar la **media** de sus valores Y

**Cómo elegir K:**
- K pequeño → muy sensible al ruido (overfitting)
- K grande → predicciones muy suavizadas (underfitting)
- Regla general: K ≈ √n (aunque hay que validarlo)

| Ventajas | Desventajas |
|----------|-------------|
| Sin entrenamiento | Costoso en datasets grandes (calcula todas las distancias) |
| Intuitivo | Funciona mal en alta dimensionalidad |
| Multi-salida nativo | Sensible a variables irrelevantes |

---

### Métodos Bayesianos de Regresión

En lugar de devolver un valor puntual, devuelven una **distribución de probabilidad** de Y.

**Idea clave — Teorema de Bayes:**
```
P(Y | X) = P(X | Y) · P(Y) / P(X)
```
- **Prior P(Y)**: creencia inicial sobre Y antes de ver los datos
- **Verosimilitud P(X|Y)**: probabilidad de los datos dado Y
- **Posterior P(Y|X)**: creencia actualizada tras ver los datos

**Proceso iterativo:** cada nuevo dato actualiza el prior → posterior que se convierte en el nuevo prior.

| Ventajas | Desventajas |
|----------|-------------|
| Cuantifica la incertidumbre | Complejo de implementar |
| Mejora con cada nuevo dato | Difícil de interpretar |

---

## Múltiples salidas

Si se quieren predecir **varias Y** simultáneamente:
1. **Aprender varios modelos independientes**: un modelo por cada Y
2. **Concatenar modelos**: la salida del primer modelo se usa como entrada del siguiente

---

## Related Topics

- [[Clasificación]]
- [[Validación de Modelos]]
- [[Preprocesado de Datos]]
- [[Redes Neuronales]]
- [[Overfitting y Underfitting]]
