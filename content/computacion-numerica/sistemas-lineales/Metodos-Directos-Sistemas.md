---
title: Métodos Directos para Sistemas Lineales
description: "Eliminación Gaussiana, factorizaciones LU, Cholesky y QR para resolver Ax=b. Incluye pivoteo y número de condición."
date: 2026-04-08
type: topic
subject: Computación Numérica
sources: [t3-sistemas-lineales-y-no-lineales.md]
tags: [gauss, LU, cholesky, QR, sistemas-lineales, pivoteo, computacion-numerica, universidad]
---

# Métodos Directos para Sistemas Lineales

## El problema

Dado $A \in \mathbb{R}^{n \times n}$ invertible y $b \in \mathbb{R}^n$, encontrar $x \in \mathbb{R}^n$ tal que:

$$Ax = b$$

La solución teórica es $x = A^{-1}b$, pero **calcular $A^{-1}$ explícitamente es costoso e innecesario**. Los métodos directos transforman el sistema en uno equivalente más fácil de resolver.

---

## Eliminación Gaussiana

### Idea

Transformar $Ax = b$ en un sistema **triangular superior** $Ux = c$ mediante operaciones elementales sobre las filas (que no cambian la solución).

Un sistema triangular superior se resuelve trivialmente de abajo hacia arriba (**sustitución regresiva**).

### Proceso

En $n-1$ etapas, se genera la secuencia:

$$A = A^{(1)} \to A^{(2)} \to \cdots \to A^{(n)} = U \quad \text{(triangular superior)}$$

En la etapa $k$, se elimina la variable $x_k$ de todas las ecuaciones $i > k$ usando:

$$a_{ij}^{(k+1)} = a_{ij}^{(k)} - \frac{a_{ik}^{(k)}}{a_{kk}^{(k)}} \cdot a_{kj}^{(k)}, \quad i > k$$

El número $m_{ik} = a_{ik}^{(k)}/a_{kk}^{(k)}$ se llama **multiplicador**.

**Coste computacional:** $\mathcal{O}(n^3/3)$ multiplicaciones/divisiones.

> [!example] Sistema 3×3
> $$\begin{pmatrix}2 & 1 & 1 \\ 4 & 3 & 3 \\ 8 & 7 & 9\end{pmatrix} \begin{pmatrix}x_1\\x_2\\x_3\end{pmatrix} = \begin{pmatrix}1\\1\\1\end{pmatrix}$$
> Etapa 1: eliminar $x_1$ de filas 2 y 3 → $m_{21} = 4/2 = 2$, $m_{31} = 8/2 = 4$.

---

## Factorización LU

### Idea

La eliminación Gaussiana equivale a factorizar $A = LU$ donde:
- $L$ = matriz triangular inferior con 1s en la diagonal (los multiplicadores $m_{ik}$)
- $U$ = matriz triangular superior resultado de Gauss

Una vez que tenemos $LU = A$, resolver $Ax = b$ se reduce a dos sistemas triangulares:
1. $Ly = b$ → **sustitución progresiva** (fácil)
2. $Ux = y$ → **sustitución regresiva** (fácil)

**Ventaja:** si hay varios vectores $b$ distintos con la misma matriz $A$, la factorización se hace una sola vez y luego cada sistema cuesta solo $\mathcal{O}(n^2)$.

---

## Pivoteo — cuando Gauss falla

### El problema

Si $a_{kk}^{(k)} = 0$ en alguna etapa, no se puede dividir → el método falla.  
Incluso si $a_{kk}^{(k)}$ es muy pequeño (no cero), la división genera multiplicadores enormes que amplifican los errores de redondeo.

### Solución: pivoteo parcial

Antes de la etapa $k$, intercambiar la fila $k$ con la fila que tenga el mayor valor absoluto en la columna $k$:

$$|a_{pk}^{(k)}| = \max_{i \geq k} |a_{ik}^{(k)}|$$

Esto garantiza que $|m_{ik}| \leq 1$ para todos los multiplicadores, controlando la propagación del error.

> [!tip] Regla práctica
> Siempre usar Gauss **con pivoteo parcial** en la práctica. La versión sin pivoteo es solo pedagógica.

---

## Factorización de Cholesky

### Cuándo se usa

Solo para matrices **simétricas** ($A = A^T$) y **definidas positivas** ($x^T A x > 0$ para todo $x \neq 0$).

### Resultado

$$A = L L^T$$

donde $L$ es triangular inferior con elementos positivos en la diagonal.

**Ventaja:** usa la mitad de operaciones que LU (aproximadamente $n^3/6$) y no necesita pivoteo.

**Aplicación típica:** sistemas de ecuaciones normales en mínimos cuadrados.

---

## Factorización QR

$$A = QR$$

donde $Q$ es **ortogonal** ($Q^T Q = I$) y $R$ es **triangular superior**.

Resolver $Ax = b$:
1. $QRx = b$
2. $Rx = Q^T b$ (porque $Q^{-1} = Q^T$, fácil de calcular)
3. Sustitución regresiva

**Ventaja:** muy estable numéricamente.  
**Desventaja:** coste doble que LU ($\mathcal{O}(2n^3/3)$).  
**Aplicación típica:** ajuste por mínimos cuadrados cuando $A$ no es cuadrada.

---

## Número de condición — ¿cuánto amplifican los errores?

### El problema

Aunque $A$ sea invertible, pequeñas perturbaciones en $b$ (por errores de medida o redondeo) pueden causar grandes cambios en la solución $x$. Esto se llama **mal condicionamiento**.

### Definición

$$\kappa(A) = \|A\| \cdot \|A^{-1}\|$$

- $\kappa(A) \approx 1$: bien condicionado (los errores no se amplifican).
- $\kappa(A) \gg 1$: mal condicionado (pequeños errores → grandes cambios en $x$).

La cota del error relativo es:

$$\frac{\|\delta x\|}{\|x\|} \leq \kappa(A) \cdot \frac{\|\delta b\|}{\|b\|}$$

> [!warning]
> Si $\kappa(A) \approx 10^k$, se pierden aproximadamente $k$ cifras de precisión en la solución. Con doble precisión (16 dígitos), si $\kappa(A) \approx 10^{12}$, solo se obtienen ~4 cifras correctas.

---

## Comparativa de métodos directos

| Método | Coste | Requisito | Cuándo usar |
|--------|-------|-----------|-------------|
| **Gauss + pivoteo** | $n^3/3$ | $A$ invertible | Caso general |
| **LU** | $n^3/3$ | $A$ invertible | Múltiples $b$ con misma $A$ |
| **Cholesky** | $n^3/6$ | $A$ simétrica def. positiva | Sistemas normales, física |
| **QR** | $2n^3/3$ | Cualquier $A$ | Estabilidad máxima, mínimos cuadrados |

---

## Related Topics

- [[Metodos-Iterativos-Sistemas]] — alternativa para sistemas grandes y dispersos
- [[Sistemas-No-Lineales]] — Newton vectorial usa la factorización LU internamente
- [[Ajuste-de-Datos]] — Cholesky y QR aparecen en mínimos cuadrados
