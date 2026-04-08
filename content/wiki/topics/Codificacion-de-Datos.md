---
title: Codificación de Datos
type: topic
subject: Sistemas Inteligentes
sources: [1.1-preprocesado.md]
tags: [codificacion, one-hot-encoding, discretizacion, sistemas-inteligentes, universidad]
---

## Definición

La codificación de datos consiste en **adaptar los datos al formato que necesitan los algoritmos de aprendizaje automático**.

Es la **fase 3 del [[Proceso KDD]]**. Los algoritmos de AA solo operan con números, por lo que cualquier columna categórica (texto, etiquetas) debe convertirse a representación numérica.

> Las técnicas de codificación se aplican tanto a la **entrada** (variables X) como a la **salida** (variable Y que queremos predecir).

## Dos direcciones de codificación

```
Categoría ──► Número   (lo más común: cuando la salida o entrada es texto)
Número    ──► Categoría (cuando convertimos regresión en clasificación)
```

---

## Categoría → Número

### Clasificación binaria

Solo hay dos clases. Se mapea directamente a `0` y `1`.

| Aprobado? | → | Aprobado? (codificado) |
|-----------|---|----------------------|
| No        |   | 0                    |
| Sí        |   | 1                    |

Ejemplo completo:

| Teoría | Prácticas | Aprobado? | → | Aprobado? |
|--------|-----------|-----------|---|-----------|
| 0.60   | -0.92     | No        |   | 0         |
| 1.00   | -0.30     | No        |   | 0         |
| -0.20  | 0.80      | Sí        |   | 1         |

---

### Clasificación multi-clase — One-Hot Encoding

Cuando hay más de dos clases **mutuamente excluyentes**, no se pueden asignar números arbitrarios (1, 2, 3...) porque el modelo interpretaría que PL-3 > PL-2 > PL-1, lo cual puede no tener sentido.

**Solución: One-Hot Encoding** → se crea una columna por clase, con `1` si pertenece a esa clase y `0` en caso contrario.

Antes:

| Teoría | Prácticas | Grupo |
|--------|-----------|-------|
| 0.60   | -0.92     | PL-1  |
| 1.00   | -0.30     | PL-3  |
| -0.20  | 0.80      | PL-2  |

Después (One-Hot):

| Teoría | Prácticas | PL-1 | PL-2 | PL-3 |
|--------|-----------|------|------|------|
| 0.60   | -0.92     | 1    | 0    | 0    |
| 1.00   | -0.30     | 0    | 0    | 1    |
| -0.20  | 0.80      | 0    | 1    | 0    |

**¿Cuándo sí se puede asignar números continuos?**
Solo si existe una **relación de orden real** entre las clases. Por ejemplo, si los grupos fueran "Bajo / Medio / Alto", sí tiene sentido asignar 1 / 2 / 3 porque hay un orden natural.

---

### Clasificación multi-etiqueta

Un ejemplo puede pertenecer a **varias clases simultáneamente**. Se aplica One-Hot igualmente, pero ahora puede haber múltiples `1` en la misma fila.

| Grupo          | → | PL-1 | PL-2 | PL-3 |
|----------------|---|------|------|------|
| PL-1, PL-2     |   | 1    | 1    | 0    |
| PL-2           |   | 0    | 1    | 0    |
| PL-3, PL-1     |   | 1    | 0    | 1    |

La diferencia respecto a multi-clase: en multi-clase exactamente un `1` por fila; en multi-etiqueta puede haber varios.

---

## Número → Categoría (Discretización)

A veces interesa **convertir un problema de regresión en clasificación**, agrupando valores numéricos en rangos con etiqueta.

**Ejemplo:** La nota final de un alumno (número continuo) se convierte en categoría:

| Nota | Categoría |
|------|-----------|
| 0–4.9 | Suspenso |
| 5.0–5.9 | Suficiente |
| 6.0–6.9 | Bien |
| 7.0–8.9 | Notable |
| 9.0–10 | Sobresaliente |

Después de discretizar, se aplica One-Hot sobre las categorías resultantes:

| Nota | Grupo      | Susp. | Suf. | Bien | Not. | Sob. |
|------|------------|-------|------|------|------|------|
| 7.8  | Notable    | 0     | 0    | 0    | 1    | 0    |
| 5.0  | Suficiente | 0     | 1    | 0    | 0    | 0    |
| 0.8  | Suspenso   | 1     | 0    | 0    | 0    | 0    |

---

## Resumen de técnicas

| Situación | Técnica |
|-----------|---------|
| Salida binaria (2 clases) | Mapeo directo → 0/1 |
| Salida multi-clase sin orden | One-Hot Encoding |
| Salida multi-clase con orden | Numérico continuo (1, 2, 3...) |
| Salida multi-etiqueta | One-Hot (múltiples 1s por fila) |
| Número continuo → categorías | Discretización + One-Hot |

## Fuentes

- [[1.1-preprocesado]]

## Related Topics

- [[Preprocesado de Datos]]
- [[Proceso KDD]]
- [[Aprendizaje Automático]]
