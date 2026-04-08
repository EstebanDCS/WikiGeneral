---
title: Wiki Overview
description: Estado general de la wiki y cobertura de contenidos.
type: overview
date: 2026-04-08
---

## Estado actual

La wiki cubre **todos los materiales disponibles de Sistemas Inteligentes**: 20 fuentes, 15 topics, 2 entidades. Cobertura completa de teoría y prácticas.

## Estructura

```
wiki/
├── sistemas-inteligentes/
│   ├── fundamentos/       → 7 temas (IA, KDD, Preprocesado, Codificación, Visualización, Reducción)
│   ├── algoritmos/        → 5 temas (Regresión, Validación, Clasificación, Redes, CNN)
│   ├── conceptos-clave/   → 3 temas (Overfitting, Desbalanceo, Herramientas Python)
│   ├── fuentes/
│   │   ├── teoria/        → 11 documentos (PDFs Temas 1.0–3.1 + ejercicio + 2 PPTXs pendientes)
│   │   └── practicas/     → 9 notebooks (P2.1–P4.3, dataset F1 Monaco 2023)
│   └── personas/          → John McCarthy, Frank Rosenblatt
```

## Cobertura de Sistemas Inteligentes

```
TEORÍA
├── Fundamentos
│   ├── Inteligencia Artificial & Aprendizaje Automático  ✓
│   ├── Proceso KDD                                        ✓
│   ├── Preprocesado de Datos                              ✓
│   ├── Codificación de Datos                              ✓
│   ├── Visualización de Datos                             ✓
│   └── Reducción de Dimensionalidad (PCA, t-SNE, LDA)    ✓
├── Algoritmos
│   ├── Regresión (lineal, polinómica, SVR, KNN, Bayes)   ✓
│   ├── Validación (hold-out, K-fold, meta-validación)     ✓
│   ├── Clasificación (logística, SVM, árboles, KNN, NB)  ✓
│   ├── Redes Neuronales (GD, dropout, Adam, mini-batch)  ✓
│   └── Redes Convolucionales (CNN, MNIST)                 ✓
└── Temas transversales
    ├── Overfitting y Underfitting                          ✓
    └── Desbalanceo de Clases                               ✓

PRÁCTICAS (dataset F1 Monaco 2023 como hilo conductor)
├── P2.1 — Entorno VSCode/Jupyter/Conda                    ✓
├── P2.2 — EDA y limpieza con Pandas                       ✓
├── P2.3 — Visualización con Matplotlib/Seaborn/Bokeh      ✓
├── P3.1 — Clasificación binaria con sklearn                ✓
├── P3.1 Extra — Desbalanceo de clases                      ✓
├── P3.2 — Regresión con sklearn                            ✓
├── P4.1 — Redes neuronales para regresión (Keras)          ✓
├── P4.2 — Redes para clasificación (binaria/multi/label)   ✓
└── P4.3 — CNN en GPU, MNIST                                ✓

PENDIENTE
├── T7 Anomalías (pptx — abrir manualmente)
└── Representación Vectorial (pptx — abrir manualmente)
```

## Preguntas abiertas

- ¿Qué contienen exactamente los PPTXs de Anomalías y Representación Vectorial?
- ¿Hay más temas o exámenes de años anteriores disponibles?
