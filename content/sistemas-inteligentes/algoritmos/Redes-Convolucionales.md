---
title: Redes Convolucionales (CNN)
description: "CNN para imágenes: capas convolucionales, pooling y flatten. Caso práctico con MNIST."
date: 2026-04-08
type: topic
subject: Sistemas Inteligentes
sources: [p4.3-cnn-practica.md, 3.1-redes-neuronales.md]
tags: [cnn, redes-convolucionales, imagenes, deep-learning, sistemas-inteligentes, universidad]
---

## Definición

Las redes convolucionales (CNN, *Convolutional Neural Networks*) son un tipo especial de [[Redes Neuronales]] diseñadas para trabajar con **datos con estructura espacial**, especialmente imágenes.

A diferencia de las redes totalmente conectadas, las CNN no conectan cada neurona con todas las anteriores — aplican **filtros convolucionales** que detectan patrones locales (bordes, texturas, formas).

---

## Por qué las redes totalmente conectadas fallan con imágenes

Una imagen de 28×28 píxeles tiene 784 valores. Una de 224×224 (ImageNet) tiene 150.528.

- Si conectamos cada píxel a cada neurona → demasiados parámetros → overfitting, lentitud
- Las redes densas no aprovechan que **los píxeles cercanos están relacionados** (un borde es una transición local)
- CNN comparte los mismos filtros por toda la imagen → muchos menos parámetros

---

## Arquitectura CNN

```
Imagen de entrada
    │
    ▼  Capa Convolucional    → detecta patrones simples (bordes, esquinas)
    │  + Activación (ReLU)
    │
    ▼  Capa de Pooling       → reduce dimensión, mantiene lo importante
    │
    ▼  Más capas Conv + Pool  → detecta patrones más complejos (formas, objetos)
    │
    ▼  Flatten               → convierte el mapa de características en un vector
    │
    ▼  Capas Dense           → clasificación final
    │
    ▼  Softmax / Sigmoid     → probabilidades por clase
```

### Capa Convolucional
Aplica un **filtro** (kernel pequeño, ej: 3×3) que se desliza por toda la imagen y detecta si hay una cierta estructura en cada posición. Varios filtros → varios canales de salida, cada uno detectando algo distinto.

### Capa de Pooling
Reduce el tamaño espacial (ej: Max Pooling 2×2 divide dimensiones por 2) manteniendo la información más relevante. Hace el modelo más robusto a pequeñas variaciones de posición.

---

## Caso práctico: MNIST

Dataset: 70.000 imágenes 28×28 de dígitos escritos a mano (0-9).

**Preparación de datos para CNN:**
```python
# Normalizar entre 0 y 1 (manual, no con StandardScaler)
X = X / 255.0

# CNN necesita dimensión del canal: (n_ejemplos, 28, 28, 1)
X = X.reshape(-1, 28, 28, 1)
```

**Comparativa de rendimiento:**

| Modelo | Accuracy (Test) | F1 (Test) |
|--------|----------------|-----------|
| Baseline Random | 11.4% | 3.2% |
| Baseline Zero-R | 11.4% | 2.0% |
| KNN | ~21% | — |
| Red totalmente conectada | mejor | — |
| **CNN** | **mejor de todos** | — |

---

## Cuándo usar CNN

| Situación | CNN |
|-----------|-----|
| Imágenes (clasificación, detección, segmentación) | ✓ Ideal |
| Audio (espectrogramas) | ✓ Funciona |
| Texto (con embeddings 2D) | A veces |
| Datos tabulares | ✗ Usar redes densas |

---

## Entrenamiento en GPU

Las CNN son computacionalmente costosas. En la asignatura se usa:
- **WSL** (Windows Subsystem for Linux) con drivers NVIDIA
- **TensorFlow-GPU** dentro del environment conda `SSII`
- Acelera el entrenamiento significativamente vs CPU

---

## Fuentes

- [[p4.3-cnn-practica]]
- [[3.1-redes-neuronales]]

## Related Topics

- [[Redes Neuronales]]
- [[Clasificación]]
- [[Reducción de Dimensionalidad]]
