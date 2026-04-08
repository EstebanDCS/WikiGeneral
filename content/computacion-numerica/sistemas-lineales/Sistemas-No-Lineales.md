---
title: Sistemas No Lineales
description: "Extensión de punto fijo y Newton-Raphson a sistemas de varias ecuaciones no lineales. Usa la matriz jacobiana."
date: 2026-04-08
type: topic
subject: Computación Numérica
sources: [t3-sistemas-lineales-y-no-lineales.md]
tags: [sistemas-no-lineales, jacobiana, newton-vectorial, computacion-numerica, universidad]
---

# Sistemas No Lineales

## El problema

Encontrar $r \in \mathbb{R}^n$ tal que $f(r) = 0$, donde $f : U \subset \mathbb{R}^n \to \mathbb{R}^n$ es un sistema de $n$ ecuaciones con $n$ incógnitas:

$$\begin{cases} f_1(x_1, x_2, \ldots, x_n) = 0 \\ f_2(x_1, x_2, \ldots, x_n) = 0 \\ \vdots \\ f_n(x_1, x_2, \ldots, x_n) = 0 \end{cases}$$

---

## Punto Fijo Vectorial

La misma idea que en una dimensión: reescribir $f(x) = 0$ como $g(x) = x$ e iterar:

$$x^{(k+1)} = g(x^{(k)})$$

La convergencia requiere que la "derivada" de $g$ (la matriz jacobiana $dg$) tenga norma $< 1$ en el punto fijo.

---

## Método de Newton Vectorial

### Idea

Aproximar $f$ por su **linealización** en $x^{(k)}$:

$$f(x) \approx f(x^{(k)}) + df(x^{(k)})(x - x^{(k)})$$

Igualar a cero:

$$f(x^{(k)}) + df(x^{(k)})(x^{(k+1)} - x^{(k)}) = 0$$

Resolver para $x^{(k+1)}$:

$$\boxed{x^{(k+1)} = x^{(k)} - [df(x^{(k)})]^{-1} f(x^{(k)})}$$

En la práctica, **nunca se calcula $[df]^{-1}$ explícitamente**. En su lugar, cada iteración resuelve un sistema lineal:

$$[df(x^{(k)})] \Delta x^{(k)} = -f(x^{(k)}), \quad x^{(k+1)} = x^{(k)} + \Delta x^{(k)}$$

### La Matriz Jacobiana $df$

La derivada de $f$ en el punto $x_0$ es la matriz de derivadas parciales:

$$df(x_0) = \begin{pmatrix} \frac{\partial f_1}{\partial x_1}(x_0) & \frac{\partial f_1}{\partial x_2}(x_0) & \cdots & \frac{\partial f_1}{\partial x_n}(x_0) \\ \frac{\partial f_2}{\partial x_1}(x_0) & \frac{\partial f_2}{\partial x_2}(x_0) & \cdots & \frac{\partial f_2}{\partial x_n}(x_0) \\ \vdots & & \ddots & \vdots \\ \frac{\partial f_n}{\partial x_1}(x_0) & \cdots & & \frac{\partial f_n}{\partial x_n}(x_0) \end{pmatrix}$$

### Coste por iteración

1. Evaluar las $n^2$ derivadas parciales $\partial f_i / \partial x_j$ en $x^{(k)}$.
2. Resolver un sistema lineal $n \times n$ → $\mathcal{O}(n^3/3)$ operaciones.

Para sistemas grandes, el coste puede ser prohibitivo → se usan variantes.

### Convergencia

Igual que en 1D: **convergencia cuadrática local** si $df(r)$ es invertible.

> [!example] Ejemplo
> Sistema: $x^2 + y^2 = 4$, $xy = 1$.
>
> $$f(x,y) = \begin{pmatrix} x^2 + y^2 - 4 \\ xy - 1 \end{pmatrix}, \quad df(x,y) = \begin{pmatrix} 2x & 2y \\ y & x \end{pmatrix}$$
>
> Partiendo de $(x_0, y_0) = (1/2, 2)$, una iteración de Newton resuelve:
> $$\begin{pmatrix}1 & 4\\ 2 & 1/2\end{pmatrix} \Delta = -\begin{pmatrix}1/4 + 4 - 4\\ 1 - 1\end{pmatrix} = -\begin{pmatrix}1/4\\ 0\end{pmatrix}$$

---

## Variantes para evitar calcular $df$ en cada paso

| Variante | Idea | Convergencia |
|---------|------|-------------|
| **Quasi-Newton** | Actualizar $df$ solo cada $p$ iteraciones | Sublineal |
| **Derivadas aproximadas** | $\partial f_i/\partial x_j \approx [f_i(x + h e_j) - f_i(x)]/h$ | Cuadrática (si $h$ bien elegido) |
| **Extensión de la secante** | Usar incrementos previos para aproximar $df$ | Superlineal |

---

## Related Topics

- [[Newton-Raphson]] — el método en 1D del que este es extensión
- [[Metodos-Directos-Sistemas]] — cada iteración de Newton resuelve un sistema lineal
- [[Punto-Fijo]] — el fundamento teórico de la convergencia
