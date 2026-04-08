---
title: Preprocesado de Datos
description: "Análisis exploratorio, detección de errores, estandarización y normalización de datos."
date: 2026-04-08
type: topic
subject: Sistemas Inteligentes
sources: [1.1-preprocesado.md]
tags: [preprocesado, limpieza, normalizacion, estandarizacion, sistemas-inteligentes, universidad]
---

## Definición

El preprocesado de datos consiste en **mejorar la calidad de los datos** con el fin de aumentar la eficacia de los modelos de aprendizaje automático.

Es la **fase 2 del [[Proceso KDD]]** y es crítica: los modelos de AA son tan buenos como los datos con los que aprenden. *Garbage in, garbage out.*

## Fases del Preprocesado

```
Datos seleccionados
    │
    ├─► Análisis        → entender qué tienes
    ├─► Limpieza        → eliminar lo malo
    └─► Normalización / Estandarización  → igualar escalas
```

---

## 1. Análisis

Antes de limpiar nada, hay que entender qué tienes delante.

### Tipo de datos

| Tipo | Ejemplos |
|------|----------|
| **Numérico** | Edades, precios, temperaturas |
| **Textual** | Nombres, descripciones, categorías |
| **Imagen** | Fotos, radiografías, mapas |
| **Vídeo** | Grabaciones, secuencias de frames |
| **Audio** | Grabaciones de voz, música |

Los conjuntos reales suelen ser **multimodales** (mezclan varios tipos).

### Formatos de archivo comunes

**Datos tradicionales:**

| Formato | Descripción |
|---------|-------------|
| `.csv` | Comma Separated Values — el más habitual |
| `.xlsx` | Excel |
| `.json` | JavaScript Object Notation |
| `.pkl` | Pickle (Python) |
| `.arff` | Attribute-Relation File Format (Weka) |
| `.sqlite` | Base de datos SQLite |

**Datos multimedia:**

| Formato | Descripción | ¿Con compresión? |
|---------|-------------|-----------------|
| `.jpeg` | Imágenes fotográficas | Sí |
| `.png` | Imágenes con transparencia | Sí/No |
| `.raw` | Imágenes en "crudo" | No |
| `.wav` | Audio sin comprimir | No |
| `.mp3` | Audio comprimido | Sí |

### Preguntas clave en el análisis

- ¿Podemos manejar los datos en un PC normal?
- ¿Caben en memoria o hay que cargarlos por partes?
- ¿Son datos en tiempo real (streaming)?
- ¿Hay suficientes datos para aprender el problema?

**Tamaños de referencia:**

| Dataset | Descripción |
|---------|-------------|
| Iris | 150 muestras, 4 características — muy pequeño |
| CIFAR-100 | 60.000 imágenes 32×32, 100 categorías |
| COCO | 330.000 imágenes, 80 categorías |
| ImageNet | 14 millones de imágenes, 1.000 categorías |
| Wikipedia | Millones de páginas, terabytes de texto |

---

## 2. Limpieza

### Errores y valores ausentes

Es muy común encontrar datos corruptos o incompletos. Hay que detectarlos y tratarlos.

**Ejemplo:** En una tabla de notas, la fila de un alumno tiene:
- El número de UO en la columna "Aprobado" (columnas intercambiadas)
- La nota de teoría en `None` (valor ausente)

| Nombre | UO     | Aprobado? | Teoría | Prácticas |
|--------|--------|-----------|--------|-----------|
| A001   | 100001 | False     | 8      | 0.04      |
| A374   | **False** | **7.6** | 0.4  | 8.0       |  ← errores
| A375   | 100375 | True      | **None** | 0.9  |  ← valor ausente

**Estrategias:**
- Corregir el error si es recuperable (ej: intercambio de columnas → restar y recalcular)
- Imputar el valor ausente (media, mediana, valor más frecuente)
- Eliminar la fila si no es recuperable

### Variables irrelevantes

Muchas columnas no aportan información útil para la predicción y añaden ruido.

**Ejemplo:** Para predecir si un alumno aprueba, el nombre y el número de UO son irrelevantes. Se eliminan.

Antes:

| Nombre | UO     | Aprobado? | Teoría | Prácticas | Suma notas |
|--------|--------|-----------|--------|-----------|------------|
| A001   | 100001 | False     | 8      | 0.04      | 8.04       |

Después (sin columnas irrelevantes):

| Aprobado? | Teoría | Prácticas | Suma notas |
|-----------|--------|-----------|------------|
| False     | 8      | 0.04      | 8.04       |

### Correlaciones y redundancias

Si dos columnas están muy correlacionadas, una es redundante. Mantener las dos puede confundir al modelo.

**Ejemplo — matriz de correlación:**

|             | Teoría | Prácticas | Suma notas |
|-------------|--------|-----------|------------|
| Teoría      | 100%   | -1%       | **73%**    |
| Prácticas   | -1%    | 100%      | **68%**    |
| Suma notas  | 73%    | 68%       | 100%       |

→ "Suma notas" es casi una combinación lineal de Teoría y Prácticas → se elimina.

Métodos para detectarlo: **Information Gain**, **Correlation-based Feature Selection (CFS)**.

---

## 3. Estandarización y Normalización

### ¿Por qué son necesarias?

Si una columna va de 0 a 10 (nota de teoría) y otra de 0 a 1 (nota de prácticas), los algoritmos que usan distancias o gradientes tratarán la primera como si fuera 10 veces más importante. Hay que **igualar las escalas**.

### Estandarización (Z-score)

Transforma los datos para que tengan **media = 0** y **desviación típica = 1**.

$$z = \frac{x - \mu}{\sigma}$$

**Ejemplo:**

| | Teoría (original) | Teoría (estandarizada) |
|-|-------------------|----------------------|
| A001 | 8.00 | 0.36 |
| A002 | 10.00 | 0.87 |
| A375 | 4.00 | -0.67 |
| Media | 6.60 | **0.00** |
| Desv. | 3.91 | **1.00** |

**Cuándo usarla:** Cuando el algoritmo asume distribución gaussiana (SVM, regresión logística, PCA).

### Normalización (Min-Max)

Escala los datos al rango **[0, 1]**:

$$x' = \frac{x - x_{min}}{x_{max} - x_{min}}$$

O al rango **[-1, 1]**:

$$x' = 2 \cdot \frac{x - x_{min}}{x_{max} - x_{min}} - 1$$

**Cuándo usarla:** Redes neuronales, cuando quieres mantener la distribución original pero cambiar la escala.

### Resumen: ¿estandarización o normalización?

| Técnica | Resultado | Mejor para |
|---------|-----------|-----------|
| Estandarización | Media=0, Desv=1 | Datos con distribución normal, SVM, PCA |
| Normalización [0,1] | Valores entre 0 y 1 | Redes neuronales, distancias |
| Normalización [-1,1] | Valores entre -1 y 1 | Redes neuronales (con simetría) |

---

## Debates & Contradictions

- No hay una regla universal para elegir entre estandarización y normalización; depende del algoritmo y la distribución de los datos.
- Hay discusión sobre cuándo vale la pena imputar valores ausentes vs eliminar las filas.

## Fuentes

- [[1.1-preprocesado]]

## Related Topics

- [[Proceso KDD]]
- [[Codificación de Datos]]
- [[Aprendizaje Automático]]
