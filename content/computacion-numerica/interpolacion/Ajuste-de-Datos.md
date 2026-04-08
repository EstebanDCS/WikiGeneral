---
title: Ajuste de Datos (Mínimos Cuadrados)
description: "Cuando los datos tienen ruido, no se interpola sino que se ajusta: encontrar la función que mejor aproxima los datos en conjunto."
date: 2026-04-08
type: topic
subject: Computación Numérica
sources: [t4-interpolacion-aproximacion-ajuste.md]
tags: [minimos-cuadrados, ajuste, regresion, computacion-numerica, universidad]
---

# Ajuste de Datos (Mínimos Cuadrados)

## Diferencia con interpolación

| | Interpolación | Ajuste (mínimos cuadrados) |
|--|--------------|---------------------------|
| **Datos** | Exactos | Con ruido o error de medida |
| **Objetivo** | Pasar por **todos** los puntos | Minimizar el error total |
| **Resultado** | $P(x_i) = f(x_i)$ exactamente | $P(x_i) \approx f(x_i)$ |
| **Cuándo usar** | Datos de función conocida | Datos experimentales |

> [!example] Intuición
> Si mides la posición de un móvil 10 veces y hay ruido en el sensor, no tiene sentido construir un polinomio de grado 9 que pase exactamente por todos los puntos. Mejor ajustar una recta o parábola que capture la tendencia.

---

## Ajuste Lineal (Regresión Lineal)

### El problema

Dados $m$ puntos $(x_1, y_1), \ldots, (x_m, y_m)$ con $m \gg n$, encontrar los coeficientes $a_0, a_1, \ldots, a_n$ de:

$$p(x) = a_0 + a_1 x + a_2 x^2 + \cdots + a_n x^n$$

que minimicen el **error cuadrático total**:

$$E = \sum_{i=1}^{m} [y_i - p(x_i)]^2$$

### Solución: Ecuaciones Normales

El mínimo de $E$ se alcanza cuando:

$$A^T A \, c = A^T y$$

donde $A$ es la **matriz de Vandermonde**:

$$A = \begin{pmatrix} 1 & x_1 & x_1^2 & \cdots & x_1^n \\ 1 & x_2 & x_2^2 & \cdots & x_2^n \\ \vdots & & & & \vdots \\ 1 & x_m & x_m^2 & \cdots & x_m^n \end{pmatrix}, \quad c = \begin{pmatrix}a_0\\a_1\\\vdots\\a_n\end{pmatrix}, \quad y = \begin{pmatrix}y_1\\y_2\\\vdots\\y_m\end{pmatrix}$$

El sistema $A^T A \, c = A^T y$ tiene $n+1$ ecuaciones y es **simétrico definido positivo** → se resuelve con Cholesky o QR.

---

## Ajuste Trigonométrico

Cuando los datos tienen **periodicidad**, se ajusta una combinación de senos y cosenos:

$$p(x) = a_0 + \sum_{k=1}^{N} (a_k \cos kx + b_k \sin kx)$$

Los coeficientes $a_k, b_k$ se calculan eficientemente con la **Transformada Discreta de Fourier (DFT)**, y su versión rápida FFT (*Fast Fourier Transform*).

---

## Ajuste No Lineal: Linealización

### El problema

Si el modelo tiene la forma $y = f(x; \theta)$ donde $\theta$ son parámetros y $f$ es **no lineal** en $\theta$:

- Ejemplo: $y = a e^{bx}$ o $y = a x^b$.

El método de mínimos cuadrados directamente llevaría a un sistema no lineal difícil.

### Solución: linealizar tomando logaritmos

Para $y = a e^{bx}$:
$$\ln y = \ln a + bx$$

Definiendo $Y = \ln y$, $A = \ln a$: tenemos el modelo lineal $Y = A + bx$.

> [!warning] Cuidado
> La linealización minimiza el error en la escala transformada ($\ln y$), no en la escala original ($y$). Para minimizar el error en la escala original hay que usar métodos iterativos de optimización no lineal.

---

## Related Topics

- [[Interpolacion-Polinomial]] — cuando se quiere pasar exactamente por los datos
- [[Metodos-Directos-Sistemas]] — la solución de las ecuaciones normales usa Cholesky o QR
