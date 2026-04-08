---
title: Proceso KDD
description: "Pipeline de 5 fases para resolver problemas de machine learning: selección, preprocesado, transformación, minería y evaluación."
date: 2026-04-08
type: topic
subject: Sistemas Inteligentes
sources: [1.0-introduccion.md, 1.1-preprocesado.md]
tags: [kdd, pipeline, sistemas-inteligentes, universidad]
---

## Definición

**KDD** = *Knowledge Discovery in Databases* (Descubrimiento de Conocimiento en Bases de Datos).

Es el **pipeline estándar** que describe las etapas necesarias para resolver un problema de aprendizaje automático de forma efectiva. Define qué hacer con los datos desde que llegan en bruto hasta que obtienes conclusiones útiles.

## Las 5 Fases

```
Datos brutos
    │
    ▼
┌─────────────────┐
│  1. Selección   │  Escoger los datos relevantes
└────────┬────────┘
         │
         ▼
┌──────────────────────┐
│  2. Preprocesamiento │  Limpiar y transformar los datos
└────────┬─────────────┘
         │
         ▼
┌──────────────────┐
│  3. Codificación │  Adaptar al formato del algoritmo
└────────┬─────────┘
         │
         ▼
┌────────────────────┐
│  4. Aprendizaje    │  Entrenar el modelo
└────────┬───────────┘
         │
         ▼
┌───────────────────────────────┐
│  5. Interpretación/Evaluación │  Juzgar la calidad del modelo
└───────────────────────────────┘
         │
         ▼
   Conocimiento / Conclusiones
```

---

### Fase 1 — Selección

- **Objetivo**: Escoger qué datos son relevantes para el problema.
- **Entrada**: Conjuntos de datos brutos (puede haber muchas tablas, fuentes, periodos)
- **Salida**: Datos seleccionados
- **Ejemplo**: De un histórico de ventas de 10 años, seleccionar solo los últimos 2 años porque el comportamiento del mercado cambió.

---

### Fase 2 — Preprocesamiento

- **Objetivo**: Limpiar y transformar los datos para que sean de calidad.
- **Entrada**: Datos seleccionados
- **Salida**: Datos preprocesados
- **Ejemplo**: Hay alumnos con la nota de teoría en `None` → rellenar o eliminar esa fila.

> Ver detalles completos en [[Preprocesado de Datos]]

---

### Fase 3 — Codificación

- **Objetivo**: Transformar los datos al formato que entienden los algoritmos de AA.
- **Entrada**: Datos preprocesados
- **Salida**: Datos codificados
- **Ejemplo**: La columna "Grupo" tiene valores `PL-1 / PL-2 / PL-3` → convertir a 3 columnas binarias (One-Hot).

> Ver detalles completos en [[Codificación de Datos]]

---

### Fase 4 — Aprendizaje

- **Objetivo**: Aplicar algoritmos de AA para aprender patrones o hacer predicciones.
- **Entrada**: Datos codificados
- **Salida**: Modelo entrenado
- **Tareas típicas**:
  - Elegir el algoritmo adecuado (árbol de decisión, red neuronal, SVM...)
  - Dividir datos en train/test (y validación)
  - Ajustar hiperparámetros

---

### Fase 5 — Interpretación y Evaluación

- **Objetivo**: Juzgar la calidad del modelo y extraer conclusiones.
- **Entrada**: Modelo entrenado
- **Salida**: Conclusiones, métricas, decisiones
- **Preguntas típicas**: ¿El modelo generaliza bien? ¿Comete errores sistemáticos? ¿Es útil para el problema real?

---

## Por qué importa el KDD

Sin un proceso estructurado es fácil:
- Usar datos de mala calidad → modelo que aprende basura
- Olvidarse de normalizar → un algoritmo penaliza más unas variables que otras sin sentido
- Evaluar en los mismos datos de entrenamiento → resultados engañosos

El KDD obliga a pensar en el problema de forma ordenada antes de ejecutar cualquier algoritmo.

## Fuentes

- [[1.0-introduccion]]
- [[1.1-preprocesado]]

## Related Topics

- [[Preprocesado de Datos]]
- [[Codificación de Datos]]
- [[Aprendizaje Automático]]
