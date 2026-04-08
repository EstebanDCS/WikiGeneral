---
title: Interpolación Polinomial
description: "Construir un polinomio que pase exactamente por unos puntos dados. Formas de Lagrange y Newton, error de interpolación, nodos de Chebyshev y splines cúbicos."
date: 2026-04-08
type: topic
subject: Computación Numérica
sources: [t4-interpolacion-aproximacion-ajuste.md]
tags: [interpolacion, lagrange, newton, chebyshev, splines, computacion-numerica, universidad]
---

# Interpolación Polinomial

## El problema

Dada una función $f$ conocida solo en $n+1$ puntos (nodos) $x_0, x_1, \ldots, x_n$, encontrar un polinomio $P_n(x)$ de grado $\leq n$ tal que:

$$P_n(x_i) = f(x_i), \quad i = 0, 1, \ldots, n$$

**¿Para qué sirve?**
- Evaluar $f$ en puntos intermedios (sin conocer $f$ explícitamente).
- Aproximar funciones complicadas por polinomios más simples.
- Base para las fórmulas de integración numérica.

**El resultado fundamental:** dado un conjunto de $n+1$ nodos distintos y valores, existe **un único** polinomio de grado $\leq n$ que los interpola.

---

## Forma de Lagrange

### Construcción

Se construyen los **polinomios base de Lagrange** $L_i(x)$ de grado $n$ que valen 1 en $x_i$ y 0 en todos los demás nodos:

$$L_i(x) = \prod_{\substack{j=0 \\ j \neq i}}^{n} \frac{x - x_j}{x_i - x_j}$$

El polinomio interpolador es:

$$P_n(x) = \sum_{i=0}^{n} f(x_i) \cdot L_i(x)$$

> [!example] Interpolación con 2 nodos (lineal)
> Nodos $x_0 = 1$, $x_1 = 3$, valores $f(1) = 2$, $f(3) = 8$.
>
> $$L_0(x) = \frac{x - 3}{1 - 3} = \frac{x-3}{-2}, \quad L_1(x) = \frac{x - 1}{3 - 1} = \frac{x-1}{2}$$
>
> $$P_1(x) = 2 \cdot \frac{x-3}{-2} + 8 \cdot \frac{x-1}{2} = -(x-3) + 4(x-1) = 3x - 1$$

### Ventaja e inconveniente

- Ventaja: fórmula explícita y elegante.
- Inconveniente: si se añade un nuevo nodo hay que recalcular todos los $L_i$ desde cero.

---

## Forma de Newton con Diferencias Divididas

### Idea

Construir el polinomio de forma incremental, añadiendo nodos uno a uno.

### Diferencias divididas

Se definen recursivamente:

$$f[x_i] = f(x_i)$$

$$f[x_i, x_{i+1}] = \frac{f[x_{i+1}] - f[x_i]}{x_{i+1} - x_i}$$

$$f[x_i, x_{i+1}, x_{i+2}] = \frac{f[x_{i+1}, x_{i+2}] - f[x_i, x_{i+1}]}{x_{i+2} - x_i}$$

En general:

$$f[x_0, x_1, \ldots, x_k] = \frac{f[x_1, \ldots, x_k] - f[x_0, \ldots, x_{k-1}]}{x_k - x_0}$$

### Polinomio de Newton

$$P_n(x) = f[x_0] + f[x_0,x_1](x-x_0) + f[x_0,x_1,x_2](x-x_0)(x-x_1) + \cdots$$

**Ventaja:** añadir un nuevo nodo $x_{n+1}$ solo requiere calcular una nueva diferencia dividida y añadir un término.

---

## Error de Interpolación

Si $f \in C^{n+1}([a,b])$, el error en un punto $x$ es:

$$f(x) - P_n(x) = \frac{f^{(n+1)}(\xi(x))}{(n+1)!} \prod_{i=0}^{n} (x - x_i)$$

para algún $\xi(x) \in (a, b)$.

> [!warning] El fenómeno de Runge
> Aumentar el grado del polinomio **no siempre reduce el error**. Con nodos equiespaciados y funciones como $f(x) = 1/(1+25x^2)$ en $[-1,1]$, el error en los extremos crece al aumentar el grado. Este comportamiento se llama **fenómeno de Runge**.

---

## Nodos de Chebyshev

Para minimizar el error de interpolación, los nodos óptimos son las **raíces del polinomio de Chebyshev** de grado $n+1$ en $[a,b]$:

$$x_k = \frac{a+b}{2} + \frac{b-a}{2} \cos\left(\frac{2k+1}{2(n+1)}\pi\right), \quad k = 0, 1, \ldots, n$$

Con estos nodos, el término $\prod_{i=0}^{n}(x - x_i)$ se minimiza, reduciendo drásticamente el fenómeno de Runge.

---

## Interpolación Lineal a Trozos

En vez de un polinomio de grado alto que pase por todos los puntos, usar **un segmento recto por cada par de nodos consecutivos**.

**Error:** $\mathcal{O}(h^2)$ donde $h = \max_i(x_{i+1} - x_i)$.

**Ventaja:** siempre converge al refinar la partición (h → 0). No tiene fenómeno de Runge.

---

## Splines Cúbicos

### El problema de la interpolación lineal a trozos

La función interpolante tiene **picos** en los nodos (la derivada no es continua). Para curvas suaves, esto es indeseable.

### Solución: splines cúbicos

Un **spline cúbico** es una función $S(x)$ que:
1. En cada subintervalo $[x_i, x_{i+1}]$, $S$ es un polinomio de grado $\leq 3$.
2. $S(x_i) = f(x_i)$ para todo $i$ (interpola).
3. $S$, $S'$ y $S''$ son **continuas** en todo $[a, b]$.

Condición 3 garantiza que la curva sea "suave" (sin picos ni cambios bruscos de curvatura).

**¿Cuándo usar?** Cuando se necesita una curva visualmente suave que pase por los datos (diseño industrial, animación, gráficas).

---

## Comparativa de métodos

| Método | Grado | Suavidad | Coste | Fenómeno de Runge |
|--------|-------|----------|-------|-------------------|
| Lagrange/Newton | Alto ($n$) | $C^\infty$ | $\mathcal{O}(n^2)$ | Sí (nodos equiesp.) |
| Chebyshev | Alto ($n$) | $C^\infty$ | $\mathcal{O}(n^2)$ | No |
| Lineal a trozos | Bajo (1) | $C^0$ | $\mathcal{O}(n)$ | No |
| Splines cúbicos | Medio (3) | $C^2$ | $\mathcal{O}(n)$ | No |

---

## Related Topics

- [[Ajuste-de-Datos]] — cuando los datos tienen ruido (no se busca pasar exactamente por ellos)
- [[Integracion-Numerica]] — las fórmulas de cuadratura se derivan interpolando $f$
- [[Ecuaciones-Algebraicas]] — el algoritmo de Horner también aparece en la evaluación de polinomios interpoladores
