---
title: Métodos Iterativos para Sistemas Lineales
description: "Jacobi y Gauss-Seidel: construyen sucesivas aproximaciones a la solución de Ax=b. Ideales para sistemas grandes y dispersos."
date: 2026-04-08
type: topic
subject: Computación Numérica
sources: [t3-sistemas-lineales-y-no-lineales.md]
tags: [jacobi, gauss-seidel, iterativos, sistemas-lineales, computacion-numerica, universidad]
---

# Métodos Iterativos para Sistemas Lineales

## ¿Por qué métodos iterativos?

Los métodos directos (Gauss, LU) tienen coste $\mathcal{O}(n^3)$. Para sistemas grandes ($n = 10^6$ o más, típicos en simulaciones de ingeniería), esto es inviable.

Los métodos iterativos parten de una estimación inicial y mejoran progresivamente. Convergen en $\mathcal{O}(n^2)$ operaciones por iteración, y si la matriz es **dispersa** (muchos ceros), cada iteración es mucho más barata.

---

## Método de Jacobi

### Idea

Para resolver $Ax = b$, despejar cada $x_i$ de la $i$-ésima ecuación usando los valores de la iteración **anterior**:

$$x_i^{(k+1)} = \frac{1}{a_{ii}} \left( b_i - \sum_{j \neq i} a_{ij} x_j^{(k)} \right)$$

Se actualizan **todas** las componentes simultáneamente al final de cada iteración.

### Algoritmo

1. Elegir $x^{(0)}$ (por ejemplo, el vector cero).
2. Para $k = 0, 1, 2, \ldots$:
   $$x_i^{(k+1)} = \frac{b_i - \sum_{j < i} a_{ij} x_j^{(k)} - \sum_{j > i} a_{ij} x_j^{(k)}}{a_{ii}}, \quad i = 1, \ldots, n$$
3. Parar cuando $\|x^{(k+1)} - x^{(k)}\| < \varepsilon$.

> [!warning] Requisito
> Los elementos de la diagonal $a_{ii}$ deben ser no nulos. Si el sistema no tiene esta propiedad, hay que reorganizar las ecuaciones.

---

## Método de Gauss-Seidel

### Diferencia con Jacobi

En Gauss-Seidel, al calcular $x_i^{(k+1)}$ se usan inmediatamente los valores **ya actualizados** de $x_1^{(k+1)}, \ldots, x_{i-1}^{(k+1)}$:

$$x_i^{(k+1)} = \frac{1}{a_{ii}} \left( b_i - \sum_{j < i} a_{ij} x_j^{(k+1)} - \sum_{j > i} a_{ij} x_j^{(k)} \right)$$

### Comparativa

| | Jacobi | Gauss-Seidel |
|--|--------|--------------|
| Actualización | Todos a la vez (al final) | En cuanto se calculan |
| Velocidad | Más lento | Generalmente ~2× más rápido |
| Paralelizable | Sí (cálculos independientes) | No (hay dependencias) |
| Memoria | Necesita dos vectores | Solo uno |

> [!tip]
> En la práctica, **Gauss-Seidel es preferible** a Jacobi por ser más rápido. Jacobi tiene interés teórico y en computación paralela.

---

## Convergencia

Ambos métodos no convergen siempre. Las condiciones suficientes más usadas son:

### Diagonal dominante estricta

Si $A$ es **estrictamente diagonal dominante** (para cada fila, el valor absoluto del elemento diagonal supera la suma de los demás):

$$|a_{ii}| > \sum_{j \neq i} |a_{ij}|, \quad \forall i$$

→ **Ambos métodos convergen** para cualquier $x^{(0)}$.

### Radio espectral

La condición necesaria y suficiente técnica es que el **radio espectral** de la matriz de iteración sea menor que 1. En la práctica, se usa la condición de diagonal dominante como criterio suficiente más sencillo.

---

## Velocidad de convergencia

Ambos son métodos de orden 1 (convergencia lineal). La velocidad depende de cómo de "dominante" sea la diagonal:

- Diagonal muy dominante → convergencia rápida.
- Diagonal apenas dominante → convergencia lenta (pueden necesitarse miles de iteraciones).

Para acelerar la convergencia se usan técnicas como **SOR** (Successive Over-Relaxation) o métodos de gradiente conjugado.

---

## Related Topics

- [[Metodos-Directos-Sistemas]] — alternativa exacta para sistemas pequeños
- [[Punto-Fijo]] — los métodos iterativos son una aplicación de punto fijo a sistemas lineales
- [[Sistemas-No-Lineales]] — extensión a sistemas no lineales
