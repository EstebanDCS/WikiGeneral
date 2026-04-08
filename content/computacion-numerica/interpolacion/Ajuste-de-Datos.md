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

<svg viewBox="0 0 440 220" width="440" height="220" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <line x1="55" y1="190" x2="410" y2="190" stroke="currentColor" stroke-width="1.2"/>
  <line x1="55" y1="190" x2="55" y2="15" stroke="currentColor" stroke-width="1.2"/>
  <text x="413" y="194" font-size="11" fill="currentColor">x</text>
  <text x="40" y="13" font-size="11" fill="currentColor">y</text>
  <circle cx="80" cy="145" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="110" cy="125" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="140" cy="140" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="170" cy="105" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="200" cy="118" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="230" cy="92" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="260" cy="100" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="290" cy="78" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="320" cy="85" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="350" cy="60" r="4" fill="#4a7fa5" opacity="0.85"/>
  <circle cx="380" cy="70" r="4" fill="#4a7fa5" opacity="0.85"/>
  <line x1="70" y1="153" x2="395" y2="58" stroke="#c0582a" stroke-width="2"/>
  <text x="358" y="50" font-size="11" fill="#c0582a">ajuste lineal</text>
  <path d="M 70,153 C 120,148 160,135 200,118 C 250,98 300,80 395,70" fill="none" stroke="#5ba85e" stroke-width="1.8" stroke-dasharray="5,3"/>
  <text x="358" y="73" font-size="10" fill="#5ba85e">(interpolación: pasa</text>
  <text x="358" y="83" font-size="10" fill="#5ba85e"> exactamente)</text>
  <line x1="80" y1="145" x2="80" y2="148" stroke="#c0582a" stroke-width="1" stroke-dasharray="2,2" opacity="0.6"/>
  <line x1="200" y1="118" x2="200" y2="112" stroke="#c0582a" stroke-width="1" stroke-dasharray="2,2" opacity="0.6"/>
  <line x1="350" y1="60" x2="350" y2="68" stroke="#c0582a" stroke-width="1" stroke-dasharray="2,2" opacity="0.6"/>
  <text x="108" y="210" font-size="10" fill="currentColor" opacity="0.6">residuos: diferencia entre dato y recta ajustada</text>
</svg>

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
