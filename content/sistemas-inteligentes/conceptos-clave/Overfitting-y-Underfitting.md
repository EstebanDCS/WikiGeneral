---
title: Overfitting y Underfitting
description: "Cómo detectar sobreajuste y subajuste con curvas de aprendizaje y cómo corregirlos."
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

<svg viewBox="0 0 490 240" width="490" height="240" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <line x1="80" y1="200" x2="460" y2="200" stroke="currentColor" stroke-width="1.2"/>
  <line x1="80" y1="10" x2="80" y2="205" stroke="currentColor" stroke-width="1.2"/>
  <text x="463" y="204" font-size="11" fill="currentColor">Épocas</text>
  <text x="22" y="115" font-size="11" fill="currentColor" transform="rotate(-90,22,115)">Error</text>
  <line x1="285" y1="92" x2="285" y2="200" stroke="currentColor" stroke-width="1" stroke-dasharray="4,3" opacity="0.35"/>
  <text x="258" y="88" font-size="10" fill="currentColor">overfitting</text>
  <path d="M 80,178 C 115,158 148,132 185,108 S 245,72 285,58 S 365,40 435,34" fill="none" stroke="#4a7fa5" stroke-width="2.5"/>
  <text x="440" y="37" font-size="11" fill="#4a7fa5">train</text>
  <path d="M 80,183 C 115,162 148,138 185,115 S 248,93 285,95 S 345,118 385,143 S 420,163 435,172" fill="none" stroke="#c0582a" stroke-width="2.5"/>
  <text x="440" y="172" font-size="11" fill="#c0582a">val</text>
  <circle cx="285" cy="95" r="4" fill="#c0582a" opacity="0.8"/>
  <text x="95" y="218" font-size="10" fill="currentColor" opacity="0.6">← underfitting</text>
  <text x="302" y="218" font-size="10" fill="currentColor" opacity="0.6">overfitting →</text>
  <line x1="90" y1="14" x2="120" y2="14" stroke="#4a7fa5" stroke-width="2.5"/>
  <text x="124" y="18" font-size="11" fill="currentColor">Error entrenamiento</text>
  <line x1="275" y1="14" x2="305" y2="14" stroke="#c0582a" stroke-width="2.5"/>
  <text x="309" y="18" font-size="11" fill="currentColor">Error validación</text>
</svg>

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
