---
title: Visualización de Datos
description: "Tipos de variables, gráficos según objetivo y errores comunes de visualización."
date: 2026-04-08
type: topic
subject: Sistemas Inteligentes
sources: [1.2-visualizacion.md]
tags: [visualizacion, graficos, eda, sistemas-inteligentes, universidad]
---

## Definición

La visualización de datos es la **conversión de información abstracta en imágenes** con el objetivo de:

- **Resumir** grandes cantidades de datos en una sola imagen
- **Extraer nueva información** que no es obvia mirando los números
- **Simplificar la comprensión** de relaciones, patrones y anomalías

En el contexto del [[Proceso KDD]], la visualización apoya principalmente la **fase 2** (análisis en el preprocesado) y la **fase 5** (interpretación de resultados).

---

## Tipos de variables

Antes de elegir un gráfico hay que saber qué tipo de variable se va a representar:

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| **Numérica continua** | Cualquier valor real | Peso: 85.3 kg, altura: 1.73 m |
| **Discreta** | Solo valores enteros | Número de hijos: 0, 1, 2, 3 |
| **Categórica** | Texto o etiquetas sin orden numérico | Color de pelo: castaño, rubio |

### Representar múltiples variables a la vez

| Variables | Cómo añadir la dimensión extra |
|-----------|-------------------------------|
| 1 variable | Eje X |
| 2 variables | Ejes X e Y |
| 3 variables | Ejes X, Y + tamaño o color del punto |
| 4 variables | Ejes X, Y + tamaño + color |

---

## Tipos de gráficos según objetivo

### 1. Distribución

Muestra los **posibles valores de una variable y su frecuencia** — ¿cómo están distribuidos los datos?

| Gráfico | Cuándo usarlo |
|---------|---------------|
| **Histograma** | Una variable numérica — ver la forma de la distribución |
| **Gráfico de barras** | Una variable categórica — ver la frecuencia de cada categoría |
| **KDE2D** | Dos variables numéricas — ver dónde se concentran los datos |

**Ejemplo — histograma de edades de jugadores de fútbol:**
- El eje X muestra rangos de edad
- El eje Y muestra cuántos jugadores hay en cada rango

---

### 2. Relación o Dependencia

Representa **cómo se relacionan dos o más variables** — ¿sube una cuando sube la otra?

| Gráfico | Cuándo usarlo |
|---------|---------------|
| **Barras** | Comparar una variable numérica entre categorías |
| **Línea** | Mostrar la relación entre dos variables numéricas ordenadas |
| **Dispersión (scatter)** | Dos variables numéricas continuas — detectar correlaciones |
| **Dispersión 3D** | Tres variables numéricas — relaciones más complejas |

**Ejemplo — dispersión de valor de mercado vs salario de jugadores:**
- Cada punto es un jugador
- Si los puntos forman una nube diagonal ascendente → correlación positiva

---

### 3. Tendencia

Muestra la **evolución de un valor a lo largo del tiempo** — ¿cómo cambia algo con el tiempo?

| Gráfico | Cuándo usarlo |
|---------|---------------|
| **Línea temporal** | Variable numérica en el eje Y, tiempo en el eje X |

**Ejemplo — popularidad de "Vacaciones" en Google Trends:**
- Se ven picos claros cada verano → estacionalidad
- Sin el gráfico, los números en tabla son incomprensibles

---

### 4. Otros tipos

- **Mapas geográficos**: datos asociados a ubicaciones (países, regiones, ciudades)
- **Mapas de calor (heatmaps)**: intensidad de un valor en una cuadrícula (ej: matriz de correlación)

---

## Consejos de buenas prácticas

### Lo que NO hacer

| Error | Por qué es malo |
|-------|----------------|
| Truncar el eje Y (empezar desde un valor distinto de 0) | Exagera visualmente las diferencias |
| Usar gráficos circulares (pie charts) para comparar | El ojo humano es muy malo comparando ángulos; usa barras |
| Sobrecargar con elementos decorativos | Distrae del mensaje principal |
| Usar muchos colores diferentes | El ojo no puede procesar más de 5-7 colores distintos fácilmente |
| Usar colores distintos para el mismo elemento en comparaciones | Confunde al lector |

### Lo que SÍ hacer

| Buena práctica | Por qué funciona |
|---------------|-----------------|
| Empezar el eje Y desde 0 | Representación honesta de las magnitudes |
| Usar **barras** para comparar valores entre categorías | Longitudes son fáciles de comparar visualmente |
| Usar **área acumulada** para mostrar proporciones del total | Se ve la parte de cada categoría sobre el todo |
| Usar **color para destacar** una zona concreta | Dirige la atención del lector |
| Mismo color, diferente estilo para el mismo elemento | Coherencia visual sin confusión |
| Siempre poner título, etiquetas de ejes y unidades | El gráfico debe ser autoexplicativo |

---

## Limitación: la dimensionalidad

Con 4 variables ya es difícil visualizar. Con 100 o 2500 variables (ej: genes, píxeles) es imposible sin reducir primero. Para eso se usa [[Reducción de Dimensionalidad]] (PCA, t-SNE) antes de visualizar.

---

## Fuentes

- [[1.2-visualizacion]]

## Related Topics

- [[Proceso KDD]]
- [[Preprocesado de Datos]]
- [[Reducción de Dimensionalidad]]
