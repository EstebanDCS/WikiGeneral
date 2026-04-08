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
| **R²** (coef. determinación) | $1 - \dfrac{SS_{res}}{SS_{tot}}$ | Proporción de varianza explicada | [0, 1] → 1 es perfecto |
| **MAE** (error absoluto medio) | $\dfrac{1}{n} \sum_{i=1}^{n} \lvert y_i - \hat{y}_i \rvert$ | Error medio en las mismas unidades que Y | 0 = perfecto |
| **RMSE** (raíz error cuadrático) | $\sqrt{\dfrac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2}$ | Como MAE pero penaliza más los errores grandes | 0 = perfecto |

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

**Simple** (1 variable):
$$\hat{y} = \beta_1 x + \beta_0$$

**Multivariante** (n variables):
$$\hat{y} = \beta_0 + \beta_1 x_1 + \beta_2 x_2 + \cdots + \beta_n x_n$$

Los coeficientes $\beta$ se calculan por **mínimos cuadrados** (minimizar $\sum (y - \hat{y})^2$).

<svg viewBox="0 0 420 260" width="420" height="260" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <line x1="65" y1="230" x2="390" y2="230" stroke="currentColor" stroke-width="1.2"/>
  <line x1="65" y1="230" x2="65" y2="20" stroke="currentColor" stroke-width="1.2"/>
  <text x="393" y="234" font-size="12" fill="currentColor">X</text>
  <text x="50" y="18" font-size="12" fill="currentColor">Y</text>
  <line x1="68" y1="204" x2="388" y2="74" stroke="#c0582a" stroke-width="2"/>
  <text x="358" y="68" font-size="11" fill="#c0582a">ŷ=β₀+β₁x</text>
  <circle cx="80" cy="198" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="100" cy="185" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="120" cy="180" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="140" cy="175" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="160" cy="162" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="180" cy="150" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="200" cy="148" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="220" cy="138" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="240" cy="132" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="260" cy="122" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="280" cy="112" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="300" cy="108" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="320" cy="95" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="340" cy="88" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="360" cy="80" r="4" fill="#4a7fa5" opacity="0.85"/>
  <line x1="80" y1="198" x2="80" y2="204" stroke="currentColor" stroke-width="1" stroke-dasharray="2,2" opacity="0.5"/>
  <line x1="200" y1="148" x2="200" y2="152" stroke="currentColor" stroke-width="1" stroke-dasharray="2,2" opacity="0.5"/>
  <line x1="300" y1="108" x2="300" y2="104" stroke="currentColor" stroke-width="1" stroke-dasharray="2,2" opacity="0.5"/>
  <text x="84" y="205" font-size="9" fill="currentColor" opacity="0.55">residuo</text>
  <circle cx="74" cy="248" r="4" fill="#4a7fa5"/>
  <text x="82" y="252" font-size="11" fill="currentColor">datos observados</text>
  <line x1="220" y1="248" x2="250" y2="248" stroke="#c0582a" stroke-width="2"/>
  <text x="254" y="252" font-size="11" fill="currentColor">recta ajustada</text>
</svg>

**Interpretación de los coeficientes:** $\beta_1$ = cambio esperado en Y por cada unidad que aumenta $x_1$.

| Ventajas | Desventajas |
|----------|-------------|
| Computacionalmente muy eficiente | Solo modela relaciones lineales |
| Fácil de interpretar | Sensible a outliers |
| Funciona bien con datasets grandes | No captura relaciones complejas |

---

### Regresión Polinómica

Extiende la lineal añadiendo potencias de X:
$$\hat{y} = \beta_0 + \beta_1 x + \beta_2 x^2 + \cdots + \beta_n x^n$$

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
| Lineal | $K(x,z) = \langle x, z \rangle$ | Datos linealmente separables |
| Polinómico | $K(x,z) = (\langle x, z \rangle + c)^d$ | Relaciones polinómicas |
| RBF (Gaussian) | $K(x,z) = \exp(-\gamma \|x-z\|^2)$ | Caso general, muy flexible |

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
- Regla general: $K \approx \sqrt{n}$ (aunque hay que validarlo)

| Ventajas | Desventajas |
|----------|-------------|
| Sin entrenamiento | Costoso en datasets grandes (calcula todas las distancias) |
| Intuitivo | Funciona mal en alta dimensionalidad |
| Multi-salida nativo | Sensible a variables irrelevantes |

---

### Métodos Bayesianos de Regresión

En lugar de devolver un valor puntual, devuelven una **distribución de probabilidad** de Y.

**Idea clave — Teorema de Bayes:**
$$P(Y \mid X) = \frac{P(X \mid Y) \cdot P(Y)}{P(X)}$$
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
