---
title: Desbalanceo de Clases
type: topic
subject: Sistemas Inteligentes
sources: [p3.1-desbalanceo-practica.md, ejercicio-metricas.md, 2.3-clasificacion.md]
tags: [desbalanceo, clasificacion, metricas, oversampling, sistemas-inteligentes, universidad]
---

## Definición

El desbalanceo de clases ocurre cuando las clases del problema de clasificación tienen **distribuciones muy distintas** — una clase tiene muchos más ejemplos que la otra.

- Ejemplo típico: detección de fraude (0.1% de transacciones son fraude), diagnóstico de enfermedades raras, detección de vueltas eliminadas en F1.

---

## Por qué el Accuracy engaña

Con un dataset donde el 95% son negativos y solo el 5% positivos:

```
Modelo que SIEMPRE predice "negativo":
  Accuracy = 95%   ← parece bueno
  Precision = 0%   ← no detecta ningún positivo
  Recall = 0%      ← no detecta ningún positivo
  F1 = 0%          ← modelo inútil
```

**El accuracy solo funciona bien cuando las clases están balanceadas.**

---

## Ejemplos reales del ejercicio de métricas

| Caso | Contexto | Accuracy | Precision | Recall | Conclusión |
|------|---------|---------|-----------|--------|-----------|
| 2 | Enfermedad rara | **90%** | **0%** | **0%** | Accuracy completamente engañosa; el único positivo no se detectó |
| 4 | Armas en aeropuerto | 60% | 33% | **100%** | Se prefiere detectar todas las armas aunque haya falsas alarmas |

---

## Qué métricas usar

| Situación | Métrica recomendada |
|-----------|-------------------|
| Los **Falsos Negativos son costosos** (no detectar la enfermedad, no detectar el arma) | **Recall** |
| Los **Falsos Positivos son costosos** (marcar email legítimo como spam) | **Precision** |
| Equilibrio entre ambos | **F1-score** |
| Comparar modelos en general | **AUC-ROC** |

---

## Soluciones al desbalanceo

### 1. Cambiar la métrica de evaluación
No usar Accuracy; usar F1, Recall, AUC-ROC según el caso.

### 2. Ajustar el peso de las clases (class_weight)
La mayoría de algoritmos de sklearn aceptan `class_weight='balanced'`, que penaliza más los errores en la clase minoritaria.

```python
DecisionTreeClassifier(class_weight='balanced')
```

### 3. Oversampling — generar ejemplos artificiales
Crear ejemplos adicionales de la clase minoritaria para equilibrar el dataset.

- Técnica más conocida: **SMOTE** (Synthetic Minority Over-sampling Technique)
- Genera puntos interpolados entre los vecinos de la clase minoritaria
- Librería: `imbalanced-learn`

### 4. Undersampling
Eliminar ejemplos de la clase mayoritaria hasta equilibrar. Riesgo: perder información.

### 5. Añadir más datos reales de la clase minoritaria
La solución ideal cuando es posible.

---

## Regla práctica

> Antes de entrenar cualquier clasificador, **siempre analizar la distribución de clases** con `data.groupby("clase").size()` o un `countplot`.

---

## Fuentes

- [[p3.1-desbalanceo-practica]]
- [[ejercicio-metricas]]
- [[2.3-clasificacion]]

## Related Topics

- [[Clasificación]]
- [[Validación de Modelos]]
- [[Overfitting y Underfitting]]
