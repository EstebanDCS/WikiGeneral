---
title: "Ejercicio — Métricas de Clasificación"
description: "Ejercicio con 4 casos reales que demuestran por qué el accuracy falla con clases desbalanceadas."
date: 2026-04-08
type: source
source_file: raw/pdfs/sistemas-inteligentes/Ejercicio_metricas.pdf
source_type: pdf
date_ingested: 2026-04-08
subject: Sistemas Inteligentes
tags: [sistemas-inteligentes, metricas, clasificacion, ejercicio, universidad]
---

## Summary

Ejercicio práctico con 4 casos reales para calcular e interpretar métricas de clasificación (Accuracy, Precision, Recall) a partir de tablas de predicciones reales vs predichas. Ilustra perfectamente cuándo cada métrica engaña o informa correctamente.

## Key Points

- **Caso 1 — Aprobado/suspenso**: Accuracy=80%, Precision=80%, Recall=80% → métricas equilibradas, clases balanceadas
- **Caso 2 — Enfermedad rara**: Accuracy=90%, Precision=0%, Recall=0% → accuracy engañosa, solo hay 1 positivo real y el modelo no lo detecta
- **Caso 3 — Spam**: Accuracy=70%, Precision=100%, Recall=40% → muy preciso pero miss muchos spams; apropiado si los FP son muy costosos
- **Caso 4 — Armas en aeropuerto**: Accuracy=60%, Precision=33%, Recall=100% → detecta todas las armas pero con muchas falsas alarmas; apropiado si los FN son muy peligrosos

## Concepts Introduced

[[Clasificación]] · [[Métricas de Evaluación]]

## Data & Evidence

| Caso | Accuracy | Precision | Recall | Conclusión |
|------|----------|-----------|--------|-----------|
| 1 (examen) | 80% | 80% | 80% | Balanceado |
| 2 (enfermedad rara) | 90% | 0% | 0% | Accuracy engaña |
| 3 (spam) | 70% | 100% | 40% | Prioriza no molestar al usuario |
| 4 (aeropuerto) | 60% | 33% | 100% | Prioriza no perder ninguna amenaza |

## Connections

- Complementa la teoría de [[2.3-clasificacion]] con casos prácticos concretos
- Demuestra el peligro del accuracy en [[Desbalanceo-de-Clases]]
