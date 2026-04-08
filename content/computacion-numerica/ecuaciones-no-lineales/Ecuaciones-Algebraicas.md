---
title: Ecuaciones Algebraicas (Polinomios)
description: "Herramientas especiales para polinomios: algoritmo de Horner para evaluarlos eficientemente, deflación para reducir grado, y método de Müller para raíces complejas."
date: 2026-04-08
type: topic
subject: Computación Numérica
sources: [t2-ecuaciones-no-lineales.md]
tags: [polinomios, horner, deflacion, muller, computacion-numerica, universidad]
---

# Ecuaciones Algebraicas (Polinomios)

Un polinomio de grado $n$ es:

$$P(x) = a_0 + a_1 x + a_2 x^2 + \cdots + a_n x^n, \quad a_n \neq 0$$

Los métodos generales (bisección, Newton...) son aplicables, pero existen herramientas específicas para polinomios que los hacen más eficientes.

---

## Propiedades útiles

- Todo polinomio de grado $n$ tiene **exactamente $n$ raíces** en $\mathbb{C}$ (Teorema Fundamental del Álgebra).
- Si $r$ es raíz real, entonces $P(x) = (x - r) Q(x)$ con $\text{grado}(Q) = n-1$.
- Si $r \in \mathbb{C}$ es raíz, también lo es $\bar{r}$ (su conjugado). Las raíces complejas vienen en pares.
- Si el grado $n$ es impar, existe al menos una raíz real.

### Acotación de las raíces

Sean $\alpha = \max\{|a_0|, \ldots, |a_{n-1}|\}/|a_n|$ y $\beta = \max\{|a_1|, \ldots, |a_n|\}/|a_0|$.

Toda raíz (real o compleja) $r$ verifica: $\dfrac{1}{1+\beta} \leq |r| \leq 1 + \alpha$.

---

## Algoritmo de Horner

### El problema

Evaluar $P(x) = a_0 + a_1 x + a_2 x^2 + \cdots + a_n x^n$ en un punto $\alpha$:
- Método directo: $n$ sumas + $(2n-1)$ multiplicaciones.
- Horner: $n$ sumas + $n$ multiplicaciones. **Casi el doble de eficiente**.

### Idea

Reescribir el polinomio agrupando factores:

$$P(x) = a_0 + x(a_1 + x(a_2 + x(\cdots + x(a_{n-1} + x \cdot a_n)\cdots)))$$

### Algoritmo

1. $b_{n-1} = a_n$
2. Para $j = n-2, n-3, \ldots, 0$: $\quad b_j = a_{j+1} + \alpha \cdot b_{j+1}$
3. $P(\alpha) = a_0 + \alpha \cdot b_0$

> [!example] Ejemplo
> $P(x) = x^3 - 6x^2 + 3x - 0.149$, evaluar en $\alpha = 4.71$ con 3 dígitos.
>
> - $b_2 = 1$
> - $b_1 = -6 + 4.71 \times 1 = -1.29$
> - $b_0 = 3 + 4.71 \times (-1.29) = -3.08$
> - $P(4.71) = -0.149 + 4.71 \times (-3.08) = -14.6$
>
> La evaluación directa acumula más error de redondeo que Horner.

### Horner también calcula $P'(\alpha)$

Por el Teorema 2.8: si $b_0, \ldots, b_{n-1}$ son los coeficientes de Horner para $\alpha$, entonces:

$$P(x) = Q(x)(x - \alpha) + P(\alpha), \quad Q(x) = b_0 + b_1 x + \cdots + b_{n-1} x^{n-1}$$

Y además $P'(\alpha) = Q(\alpha)$, que se calcula con **una segunda pasada de Horner sobre los $b_j$**. Esto es especialmente útil para Newton-Raphson en polinomios.

---

## Deflación

Una vez encontrada una raíz $r$ de $P(x)$, sabemos que:

$$P(x) = (x - r) \cdot Q(x)$$

Las raíces de $Q(x)$ (de grado $n-1$) son también raíces de $P(x)$. Podemos encontrar la siguiente raíz trabajando sobre $Q$, que es más simple.

**Cálculo de $Q$:** directamente del algoritmo de Horner (los coeficientes $b_j$ son los coeficientes de $Q$).

> [!warning] Error acumulado
> Si $r$ se conoce solo aproximadamente, la deflación introduce errores que se acumulan. Para minimizarlos, se recomienda calcular las raíces de $P$ con alta precisión antes de deflactar.

---

## Método de Müller

### ¿Por qué existe?

Newton-Raphson y la secante solo encuentran **raíces reales** (sus iterados son siempre reales). Müller puede encontrar **raíces complejas** partiendo de puntos reales.

### Idea

Dados tres puntos $x_0, x_1, x_2$, construir el **polinomio de grado 2** (parábola) que pasa por $(x_0, P(x_0))$, $(x_1, P(x_1))$, $(x_2, P(x_2))$.

La siguiente aproximación $x_3$ es la **raíz de la parábola más cercana a $x_2$**.

Usar la fórmula cuadrática (que puede dar raíces complejas) permite encontrar raíces complejas del polinomio original.

**Convergencia:** orden aproximado 1.84 (entre secante y Newton).

---

## Related Topics

- [[Newton-Raphson]] — el método base que se combina con Horner
- [[Biseccion-y-Regula-Falsi]] — para obtener estimaciones iniciales de las raíces reales
- [[Interpolacion-Polinomial]] — otra perspectiva sobre el trabajo con polinomios
