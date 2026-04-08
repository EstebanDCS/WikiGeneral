---
title: Redes Neuronales
description: Arquitectura, gradient descent, Dropout, Data Augmentation, Early Stopping, mini-batches y Adam.
date: 2026-04-08
type: topic
subject: Sistemas Inteligentes
sources: [3.1-redes-neuronales.md]
tags: [redes-neuronales, deep-learning, gradient-descent, sistemas-inteligentes, universidad]
---

## Definición

Una red neuronal es un modelo de [[Aprendizaje Automático]] formado por múltiples **perceptrones** organizados en capas. Es capaz de aprender cualquier función (lineal o no lineal) a partir de datos.

- Introducidas por [[Frank-Rosenblatt]] en 1957 con el primer perceptrón
- Sirven para regresión, clasificación y problemas no estructurados (imágenes, texto, audio)

---

## El Perceptrón — unidad básica

```
x₁ ─┐
x₂ ─┤→ [Σ w·x + b] → g(z) → salida
x₃ ─┘
```

Cada perceptrón calcula: `salida = g(w₁·x₁ + w₂·x₂ + ... + b)`

Donde:
- **w**: pesos (parámetros que se aprenden)
- **b**: bias o término independiente
- **g**: función de activación

Un solo perceptrón solo puede aprender funciones lineales. Para relaciones complejas necesitamos varias capas.

---

## Arquitectura

### Capas

| Capa | Función |
|------|---------|
| **Capa de entrada** | Recibe las variables X (una neurona por variable) |
| **Capas ocultas** | Transformaciones intermedias; extraen características |
| **Capa de salida** | Produce la predicción final |

Una red con muchas capas ocultas se llama **red profunda (Deep Neural Network)**.

### Funciones de Activación

Sin funciones de activación no lineales, componer capas lineales sigue dando una función lineal. Las funciones de activación son las que permiten aprender relaciones complejas.

| Función | Forma | Rango | Uso típico |
|---------|-------|-------|-----------|
| **Sigmoide** | 1/(1+e^(-x)) | (0, 1) | Última capa clasificación binaria |
| **Tanh** | (eˣ-e^(-x))/(eˣ+e^(-x)) | (-1, 1) | Capas ocultas (centrada en 0) |
| **ReLU** | max(0, x) | [0, ∞) | Capas ocultas (la más usada) |
| **Leaky ReLU** | max(0.01x, x) | (-∞, ∞) | Capas ocultas (evita "neuronas muertas") |
| **Softmax** | eˣᵢ/Σeˣⱼ | (0,1), suma=1 | Última capa multi-clase |

### Función de activación según tipo de problema

| Problema | Activación última capa | Función de pérdida |
|----------|----------------------|-------------------|
| Regresión | Ninguna (o ReLU si Y≥0) | MSE (Error Cuadrático Medio) |
| Clasificación binaria | Sigmoide | Binary Cross-Entropy |
| Clasificación multi-clase | Softmax | Categorical Cross-Entropy |
| Clasificación multi-etiqueta | Sigmoide | Binary Cross-Entropy |

---

## Entrenamiento

### Parámetros vs Hiperparámetros

| | Qué es | Se aprende cómo |
|--|--------|----------------|
| **Parámetros** | Pesos (w) y biases (b) | Automáticamente con gradient descent |
| **Hiperparámetros** | Learning rate, epochs, arquitectura... | Los elige el programador / búsqueda |

### Gradient Descent — Descenso del Gradiente

El proceso de entrenamiento repite:

```
1. FORWARD: pasar los datos por la red → obtener predicciones
2. LOSS: calcular el error (ej: MSE entre predicciones y valores reales)
3. BACKWARD: calcular el gradiente de la loss respecto a cada peso
4. UPDATE: ajustar los pesos en la dirección contraria al gradiente
           w = w - α · ∇L(w)   (α = learning rate)
5. Repetir hasta convergencia
```

**Learning rate (α):**
- Muy alto → los pesos "saltan" y no converge
- Muy bajo → converge muy lentamente
- Técnica: reducir α gradualmente conforme avanza el entrenamiento: `α = α₀ / (1 + decay·epoch)`

---

## Underfitting y Overfitting

| | Underfitting | Overfitting |
|--|-------------|-------------|
| **Qué ocurre** | El modelo no aprende ni los datos de entrenamiento | El modelo memoriza train pero falla en test |
| **Error en train** | Alto | Bajo |
| **Error en test** | Alto | Alto |
| **Solución** | Más épocas, más neuronas/capas, otra arquitectura | Dropout, Data Augmentation, Early Stopping |

---

## Técnicas para Evitar el Overfitting

### 1. Dropout

En cada iteración de entrenamiento, se **deshabilitan aleatoriamente** un porcentaje de neuronas.

- Efecto: similar a entrenar un ensemble de redes más pequeñas
- Porcentajes típicos: 20% entrada, 25-50% capas ocultas; nunca en la capa de salida
- Las capas pueden tener diferentes tasas de dropout

**Por qué funciona:** los modelos más simples tienen menos riesgo de sobreajustarse.

### 2. Data Augmentation

Incrementar artificialmente el número de ejemplos de entrenamiento aplicando transformaciones a los datos existentes.

- Muy usado en visión por computador: rotaciones, recortes, espejos, cambios de brillo
- Un ejemplo original puede generar 9 o más variantes

### 3. Early Stopping (Parada Temprana)

Monitorizar el error en el conjunto de **validación (DEV)** durante el entrenamiento. Detener cuando el error de validación deje de mejorar (antes de que empiece a subir por overfitting).

```
Época     Error Train   Error Val
1          0.8           0.85
10         0.4           0.42
50         0.15          0.20
100        0.05          0.35  ← ¡aquí se detiene!
```

---

## Técnicas para Acelerar el Entrenamiento

### 1. Learning Rate Decay
Reducir el learning rate conforme avanza el entrenamiento para hacer ajustes más finos al converger.

### 2. Estandarización de Variables
Estandarizar los datos de entrada acelera masivamente el entrenamiento. Ejemplo del PDF:
- Sin estandarizar: 15.000 iteraciones, error 17%
- Con estandarización: 3.000 iteraciones, error 4%

### 3. Mini-Batch Gradient Descent
En lugar de actualizar los pesos con todo el dataset (lento) o con un solo ejemplo (ruidoso), procesar en **lotes (mini-batches)**:

| Variante | Tamaño batch | Característica |
|---------|-------------|---------------|
| Batch GD | n (todo) | Preciso, lento, mucha memoria |
| SGD (Estocástico) | 1 | Rápido, muy ruidoso |
| Mini-batch GD | 1 < m < n | Balance óptimo |

Tamaño de mini-batch: potencia de 2 (128, 256, 512...) por eficiencia de hardware.

### 4. Optimizador Adam
Combina dos mejoras al gradient descent:
- **Momentum**: acumula velocidad en la dirección correcta (evita oscilaciones)
- **RMSprop**: adapta el learning rate por parámetro

Adam es el optimizador estándar en la práctica para la mayoría de problemas.

---

## Resumen: ¿Cuándo usar redes neuronales?

**Úsalas cuando:**
- Los datos son no estructurados (imágenes, texto, audio)
- Las relaciones son muy complejas y no lineales
- Tienes muchos datos

**Ten cuidado con:**
- Requieren más datos que los modelos clásicos
- Son más difíciles de interpretar ("caja negra")
- Computacionalmente más costosas de entrenar

## Fuentes

- [[3.1-redes-neuronales]]

## Related Topics

- [[Aprendizaje Automático]]
- [[Clasificación]]
- [[Regresión]]
- [[Overfitting y Underfitting]]
- [[Validación de Modelos]]
- [[Preprocesado de Datos]]
