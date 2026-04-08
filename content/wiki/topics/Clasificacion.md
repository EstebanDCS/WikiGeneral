---
title: Clasificación
type: topic
subject: Sistemas Inteligentes
sources: [2.3-clasificacion.md]
tags: [clasificacion, machine-learning, svm, arboles-decision, knn, naive-bayes, sistemas-inteligentes, universidad]
---

## Definición

La clasificación predice una **variable categórica** (clase) a partir de una o varias variables de entrada.

- Ejemplos: ¿Este email es spam? ¿Qué dígito aparece en la imagen? ¿Qué tipo de flor es esta?
- Es [[Aprendizaje Automático]] **supervisado**.

---

## Métodos

### Baselines — Referencia mínima

| Baseline | Predicción | Cuándo supera al modelo real → problema |
|---------|-----------|----------------------------------------|
| **Aleatorio** | 0 o 1 al azar | Si tu modelo no supera el 50% en binaria |
| **Zero-R** | Siempre la clase mayoritaria | Si accuracy ≈ % de la clase mayoritaria |
| **One-R** | Una regla simple sobre el mejor atributo | Si tu modelo no mejora una regla de 1 variable |

**Ejemplo Zero-R:** Si el 70% de los alumnos suspendes, predecir siempre "suspenso" da 70% de accuracy. Tu modelo tiene que superar eso.

---

### Regresión Logística

Extiende la regresión lineal para clasificación **binaria** añadiendo una función sigmoide:

```
z = β₁·x + β₀           (combinación lineal)
P(y=1) = 1 / (1 + e^(-z))  (sigmoide → probabilidad entre 0 y 1)
```

**Umbral de decisión:** si P(y=1) ≥ 0.5 → predice clase 1; si no → clase 0.

**Función de pérdida:** Binary Cross-Entropy (BCE), no RMSE — porque BCE es sensible a cambios en probabilidades, RMSE no lo es para clasificación.

| Ventajas | Desventajas |
|----------|-------------|
| Funciona bien con pocos datos | Muy sensible al ruido |
| Matemáticamente simple | No modela relaciones complejas |
| Produce probabilidades | Mal en espacios de alta dimensión |

---

### SVM — Support Vector Machine

Busca el **hiperplano que maximiza el margen** entre las dos clases.

```
Clase +1  ·  ·  ·
                   ← margen →
Clase -1  ×  ×  ×
```

**Conceptos clave:**
- **Vectores soporte**: los puntos más cercanos al hiperplano (los que lo definen)
- **Margen duro**: no permite ningún error (solo datos perfectamente separables)
- **Margen blando**: permite algunos errores controlados por el hiperparámetro **C**
  - C alto → poca tolerancia al error → más overfitting
  - C bajo → más tolerancia → modelo más simple

**Kernels para datos no lineales** (igual que en SVR):

| Kernel | Cuándo |
|--------|--------|
| Lineal | Datos linealmente separables |
| Polinómico | Relaciones polinómicas |
| RBF | Caso general (más usado) |

| Ventajas | Desventajas |
|----------|-------------|
| Robusto frente a outliers | Lento en datasets grandes |
| Efectivo en alta dimensión | Difícil elegir kernel |
| Flexible con kernels | Requiere normalización |

---

### Árboles de Decisión

Estructura jerárquica: cada **nodo interno** aplica una condición sobre un atributo; las **hojas** son las predicciones.

**Funcionamiento ("divide y vencerás"):**
1. Seleccionar el atributo que mejor separa las clases → el de mayor **ganancia de información**
2. Dividir el dataset según ese atributo
3. Repetir recursivamente en cada rama
4. Parar cuando: todos los ejemplos son de la misma clase, no quedan más atributos, o se alcanza la profundidad máxima

**Selección del mejor atributo:**
- **Entropía**: mide la incertidumbre — alta si las clases están mezcladas, baja si están separadas
- **Ganancia de información**: cuánto reduce la entropía usar ese atributo

**Overfitting y poda:**
- Árboles muy profundos memorizan los datos (un camino por instancia)
- **Pre-poda**: limitar profundidad, nodos, o mínimo de ganancia de información
- **Post-poda**: construir el árbol completo y luego eliminar subárboles que no mejoran el error

| Ventajas | Desventajas |
|----------|-------------|
| Visualizable e interpretable | Propenso a overfitting |
| Maneja variables numéricas y categóricas | Sensible a variaciones pequeñas |
| Transparente (auditable) | Sesgado en clases desbalanceadas |

---

### KNN Clasificación — K-Vecinos Más Próximos

Para clasificar un nuevo punto:
1. Calcular distancia a todos los puntos de train (Euclídea, Manhattan, Coseno, Minkowski...)
2. Seleccionar los K más cercanos
3. Asignar la clase por votación

**Estrategias de votación:**
- **Mayoría**: la clase más repetida entre los K vecinos
- **Ponderada**: cada vecino tiene un peso (ej: inversamente proporcional a la distancia)

**Cómo elegir K:**
- **Validación cruzada**: probar varios K y quedarse con el mejor
- **Método del codo**: gráfica de error vs K, elegir el "codo"
- **Regla general**: K ≈ √n (no siempre óptimo)

**Importante:** siempre normalizar/estandarizar los datos antes de usar KNN, porque usa distancias.

---

### Naive Bayes

Aplica el **teorema de Bayes** asumiendo que todos los atributos son **independientes** entre sí.

```
P(Y | X₁,...,Xₙ) ∝ P(Y) · P(X₁|Y) · P(X₂|Y) · ... · P(Xₙ|Y)
```

**Componentes:**
- **Prior P(Y)**: frecuencia de cada clase en train
- **Verosimilitud P(Xᵢ|Y)**: probabilidad de cada atributo dado la clase
  - Para atributos discretos: frecuencia relativa
  - Para atributos continuos: asumir distribución Normal (o Multinomial, o Bernoulli)
- **Posterior**: combinar todo con Bayes → la clase con mayor posterior gana

**Ejemplo:** predecir si un estudiante aprueba dados las horas de estudio:
- P(aprueba) = 3/5 = 0.6
- P(3h | aprueba) = densidad gaussiana con μ=4.4, σ=0.7 → 0.08
- P(aprueba | 3h de estudio) → normalizar → 14%

| Ventajas | Desventajas |
|----------|-------------|
| Eficiente en tiempo y memoria | La asunción de independencia raramente es cierta |
| Funciona bien en texto | Complejo de implementar bien |
| Buenos resultados en varios dominios | Interpretación compleja |

---

## Métricas de Evaluación

### Matriz de Confusión

Para clasificación binaria, toda la información cabe en una tabla 2×2:

|  | Predicho: Positivo | Predicho: Negativo |
|--|-------------------|--------------------|
| **Real: Positivo** | TP (True Positive) | FN (False Negative) |
| **Real: Negativo** | FP (False Positive) | TN (True Negative) |

### Métricas derivadas

| Métrica | Fórmula | Qué mide | Cuándo usarla |
|---------|---------|---------|--------------|
| **Accuracy** | (TP+TN)/(TP+FP+TN+FN) | % ejemplos correctos | Clases balanceadas |
| **Precision** | TP/(TP+FP) | De los que dije que eran positivos, ¿cuántos lo eran? | Cuando los FP son costosos (ej: spam) |
| **Recall** | TP/(TP+FN) | De los positivos reales, ¿cuántos detecté? | Cuando los FN son costosos (ej: diagnóstico médico) |
| **F1** | 2·(P·R)/(P+R) | Media armónica de Precision y Recall | Clases desbalanceadas |
| **AUC-ROC** | Área bajo curva ROC | Rendimiento en todos los umbrales | Comparar modelos |

**Trampa del accuracy:** si el 95% de ejemplos son clase A, un modelo que siempre predice A tiene 95% de accuracy, pero es inútil para detectar la clase B.

### AUC-ROC en detalle

La curva ROC enfrenta la **Tasa de Verdaderos Positivos (TPR)** vs la **Tasa de Falsos Positivos (FPR)** para cada umbral de decisión posible.

- **AUC = 1**: modelo perfecto
- **AUC = 0.5**: equivale a un modelo aleatorio (línea diagonal)
- **AUC < 0.5**: el modelo es peor que el azar

---

## Multi-clase y Multi-etiqueta

### Adaptar clasificadores binarios a multi-clase

| Método | Cómo | Modelos necesarios |
|--------|------|-------------------|
| **One-vs-All (OvA)** | Un modelo por clase: "¿es esta clase o no?" → gana la de mayor probabilidad | n modelos |
| **One-vs-One (OvO)** | Un modelo por par de clases → votación | n·(n-1)/2 modelos |

Árboles de decisión, KNN y Naive Bayes soportan multi-clase de forma nativa.

### Adaptar a multi-etiqueta

| Método | Cómo |
|--------|------|
| **Binary Relevance (BR)** | Un clasificador binario por etiqueta, independientes entre sí |
| **Classifier Chains (CC)** | Los clasificadores están encadenados: la salida del anterior es entrada del siguiente → captura correlaciones entre etiquetas |

---

## Fuentes

- [[2.3-clasificacion]]

## Related Topics

- [[Regresión]]
- [[Validación de Modelos]]
- [[Redes Neuronales]]
- [[Overfitting y Underfitting]]
- [[Codificación de Datos]]
