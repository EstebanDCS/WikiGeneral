---
title: Reducción de Dimensionalidad
description: PCA, t-SNE y LDA: cómo reducir dimensiones sin perder información relevante.
date: 2026-04-08
type: topic
subject: Sistemas Inteligentes
sources: [1.3-reduccion.md]
tags: [reduccion-dimensionalidad, pca, t-sne, lda, sistemas-inteligentes, universidad]
---

## Definición

La reducción de dimensionalidad es el proceso de **disminuir el número de variables de un dataset manteniendo la información relevante**.

- **Dimensión** = número de variables o características del conjunto
- El número de dimensiones ha crecido enormemente con la tecnología: imágenes (píxeles), datos biomédicos (genes), sensores (parámetros)...

### ¿Por qué reducir?

| Objetivo | Explicación |
|----------|-------------|
| **Simplificar y comprimir** | Menos variables → más manejable |
| **Mejorar eficiencia computacional** | Entrenar modelos es más rápido |
| **Eliminar características irrelevantes** | Menos ruido → mejor generalización |
| **Visualizar datos** | Solo se pueden dibujar 2-3 dimensiones |

**Ejemplo motivador:** 5000 células, cada una descrita por 2500 genes. Queremos ver visualmente si hay grupos de células similares. Es imposible sin reducir a 2D.

---

## PCA — Análisis de Componentes Principales

### ¿Qué es?

Técnica de reducción **lineal** que encuentra combinaciones lineales de las variables originales (llamadas **Componentes Principales, PC**) que capturan la **mayor varianza** posible.

Cada PC es una combinación lineal de todas las variables originales:
```
PC1 = w₁·Gen_A + w₂·Gen_B + w₃·Gen_C + ... + wₙ·Gen_N
```

Donde los pesos (w) se calculan automáticamente para maximizar la varianza capturada.

### Cómo funciona — paso a paso (de 2D a 1D)

1. **Estandarizar los datos** → media=0, desviación=1 (imprescindible, si no las escalas sesgan el resultado)
2. **Buscar la recta de mejor ajuste** → la que minimiza la distancia de todos los puntos a ella; debe pasar por el origen
3. **Obtener el eigenvector** → los pesos de cada variable en esa recta (ej: `PC1 = 0.82·Gen_A + 0.57·Gen_B`)
4. **Proyectar todos los puntos** sobre esa recta → cada punto queda reducido a 1 valor

**Para pasar de 3D a 2D:** se obtienen 2 PCs, cada una perpendicular a la anterior.

**Regla importante:** PCA siempre calcula **tantas PCs como dimensiones originales** (2500 PCs si hay 2500 genes) y luego selecciona las N mejores.

### Varianza explicada

Cada PC explica una proporción de la varianza total del dataset:

- PC1 siempre explica la mayor parte
- PC2 es la segunda, etc.
- Entre todas suman el 100%

Se calcula proyectando los puntos en cada PC y midiendo la suma de cuadrados de las distancias al origen.

**Criterio de selección:** conservar las PCs que acumulen suficiente varianza explicada (ej: 95%).

### Limitaciones de PCA

| Problema | Consecuencia |
|----------|-------------|
| Solo captura relaciones **lineales** | Falla con datos con estructura curva o circular |
| No está optimizado para 2D | A veces necesita 3 o más PCs para separar bien los grupos |

---

## t-SNE — T-Distributed Stochastic Neighbour Embedding

### ¿Qué es?

Técnica de reducción **no lineal**, especialmente diseñada para **visualización en 2D**. A diferencia del PCA, no intenta maximizar la varianza global sino **mantener las relaciones de vecindad local**: puntos cercanos en el espacio original deben quedar cerca en 2D.

### Cómo funciona — paso a paso (de 2D a 1D)

1. **Calcular distancias** entre todos los pares de puntos en el espacio original
2. **Escalar las distancias según la perplejidad** → controla cuántos vecinos se consideran en cada grupo
3. **Ubicar los puntos aleatoriamente** en el nuevo espacio reducido (1D o 2D)
4. **Mover los puntos iterativamente** para que las distancias en el nuevo espacio se parezcan a las del original
5. **Parar** cuando se alcanza el número de iteraciones o no hay mejora

### El parámetro perplejidad

La **perplejidad** controla cuántos vecinos cercanos se tienen en cuenta al construir la distribución de probabilidad:
- Perplejidad baja → solo vecinos muy cercanos importan → grupos muy compactos
- Perplejidad alta → se tienen en cuenta vecinos más lejanos → grupos más difusos

Hay que ajustarla experimentalmente; no hay un valor universal.

### Advertencias importantes

| Lo que t-SNE hace | Lo que t-SNE NO hace |
|-------------------|---------------------|
| Preservar vecindad **local** | Preservar distancias **globales** |
| Puntos muy próximos → información válida | Distancias entre clusters → sin significado |
| Separar grupos visualmente en 2D | Tamaños de clusters → sin significado |

---

## PCA + t-SNE: la combinación estándar

En la práctica, se usan juntos:

```
Datos originales (N dimensiones)
    │
    ▼  PCA
Datos reducidos (~50 dimensiones)   ← elimina ruido, más rápido
    │
    ▼  t-SNE
Datos en 2D                         ← visualización final
```

**¿Por qué esta combinación?**

| Técnica | Ventaja | Desventaja |
|---------|---------|------------|
| PCA | Rápido, gestiona ruido bien | Malo en 2D (solo lineal) |
| t-SNE | Excelente en 2D, no lineal | Lento, no gestiona ruido |

---

## Otras técnicas

| Técnica | Tipo | Cuándo usarla |
|---------|------|---------------|
| **LDA** (Linear Discriminant Analysis) | Lineal, **supervisado** | Clasificación: maximiza separación entre clases y minimiza dispersión interna. Necesita etiquetas. |
| **ICA** (Independent Component Analysis) | Lineal | Separar señales mezcladas (ej: separar voces de un audio) |
| **NMF** (Non-negative Matrix Factorization) | Lineal | Datos no negativos (ej: imágenes, texto) |
| **Autoencoders** | No lineal (red neuronal) | Compresión de datos; aprende una representación compacta |

### LDA en detalle

LDA proyecta los datos en un espacio de menor dimensión buscando **dos objetivos simultáneos**:
1. Que las muestras de **diferentes clases estén lo más separadas posible**
2. Que las muestras de la **misma clase estén lo más juntas posible**

Es **supervisado**: necesita saber a qué clase pertenece cada muestra. PCA no necesita etiquetas.

---

## Aplicaciones reales

### 1. Visualización
Reducir a 2D para ver si hay grupos naturales en los datos.
- Ejemplo: dataset MNIST (imágenes de dígitos escritos a mano) visualizado en 2D con PCA + t-SNE → los dígitos del mismo número se agrupan

### 2. Eliminar características irrelevantes
Usar la varianza explicada de PCA para quedarse solo con las componentes que aportan información real y descartar las que capturan ruido.

### 3. Compresión de datos
Los autoencoders aprenden a comprimir una imagen en una representación de pocas dimensiones y luego reconstruirla. La capa intermedia es la representación comprimida.

---

## Debates & Contradictions

- PCA vs t-SNE: no hay un ganador universal; depende del objetivo (exploración vs clasificación posterior)
- La elección de perplejidad en t-SNE es crítica y no tiene una regla fija
- LDA es supervisado, lo que lo hace más potente para clasificación pero inutilizable si no hay etiquetas

## Fuentes

- [[1.3-reduccion]]

## Related Topics

- [[Preprocesado de Datos]]
- [[Visualización de Datos]]
- [[Proceso KDD]]
- [[Aprendizaje Automático]]
