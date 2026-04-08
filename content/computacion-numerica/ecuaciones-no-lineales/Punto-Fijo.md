---
title: Método del Punto Fijo
description: "Reformular f(x)=0 como g(x)=x e iterar. Convergencia garantizada si |g'(r)| < 1."
date: 2026-04-08
type: topic
subject: Computación Numérica
sources: [t2-ecuaciones-no-lineales.md]
tags: [punto-fijo, iteracion, convergencia, computacion-numerica, universidad]
---

# Método del Punto Fijo

## Idea central

En vez de resolver $f(x) = 0$ directamente, buscamos una función $g$ tal que:

$$g(x) = x \iff f(x) = 0$$

Un punto $r$ donde $g(r) = r$ se llama **punto fijo** de $g$. Si encontramos ese punto fijo, hemos resuelto el problema original.

**La iteración:** partiendo de un $x_0$ cualquiera, repetimos:

$$x_{n+1} = g(x_n)$$

Si la sucesión converge, converge al punto fijo $r$.

---

## ¿Cómo construir $g$?

Hay infinitas formas de reescribir $f(x) = 0$ como $g(x) = x$. Por ejemplo, para $f(x) = x^3 + 4x^2 - 10 = 0$:

| Reescritura | $g(x)$ | ¿Converge? |
|------------|--------|-----------|
| $x = \sqrt[3]{10 - 4x^2}$ | $g_1(x) = \sqrt[3]{10 - 4x^2}$ | Depende de la zona |
| $x = \sqrt{\frac{10 - x^3}{4}}$ | $g_2(x) = \sqrt{\frac{10 - x^3}{4}}$ | A veces sí |

No todas las elecciones convergen. La clave está en el teorema de convergencia.

---

## Teorema de Convergencia Local

> Si $g : [a, b] \to \mathbb{R}$ tiene derivada continua y $r \in [a, b]$ es punto fijo con $|g'(r)| < 1$, entonces existe $\delta > 0$ tal que si $x_0 \in (r - \delta, r + \delta)$, la sucesión $x_{n+1} = g(x_n)$ **converge a $r$**.

**Interpretación geométrica:** la condición $|g'(r)| < 1$ significa que $g$ "aplana" la curva en el punto fijo. Si la pendiente fuera mayor que 1, la iteración divergiría.

### Velocidad de convergencia

Si $|g'(r)| \neq 0$:

$$\lim_{n \to \infty} \frac{e_{n+1}}{e_n} = |g'(r)|$$

Esto es **convergencia de orden 1 (lineal)**: cada iteración reduce el error en un factor $|g'(r)|$.

> [!example] Comparativa
> Para $f(x) = x^3 + 4x^2 - 10$ con $r \approx 1.3652$:
> - Método del punto fijo con $g(x) = \sqrt[3]{10 - 4x^2}$: necesita **~30 iteraciones** para 10 dígitos.
> - Newton-Raphson: necesita **4 iteraciones** para 10 dígitos.

---

## Teorema de No Convergencia

> Si $|g'(r)| > 1$, las únicas sucesiones $x_{n+1} = g(x_n)$ que convergen a $r$ son las que tienen $x_n = r$ a partir de algún índice.

**Regla práctica:** si $|g'(r)| > 1$, no tiene sentido aplicar el método del punto fijo con esa $g$.

---

## Interpretación geométrica

La iteración $x_{n+1} = g(x_n)$ equivale a:
1. Partir de $x_n$ en el eje X.
2. Subir verticalmente hasta la curva $y = g(x)$ → obtienes el punto $(x_n, g(x_n))$.
3. Moverse horizontalmente hasta la recta $y = x$ → obtienes el punto $(g(x_n), g(x_n))$.
4. El punto fijo es la intersección de $y = g(x)$ con $y = x$.

```
y = x  ←— recta bisectriz
y = g(x) ←— la función de iteración

        ╱│
       ╱ │
──────╱──●── punto fijo r (donde se cruzan)
     ╱ ╱
    ╱ ╱  ← la espiral de la iteración converge
   ╱╱
```

Cuando $|g'(r)| < 1$: la espiral converge. Cuando $|g'(r)| > 1$: la espiral diverge.

---

## Relación con Newton-Raphson

El método de Newton es un caso especial de punto fijo con:

$$g(x) = x - \frac{f(x)}{f'(x)}$$

Esta elección especial consigue que $g'(r) = 0$, lo que da **convergencia cuadrática** (ver [[Newton-Raphson]]).

---

## Related Topics

- [[Biseccion-y-Regula-Falsi]] — métodos de intervalo, más seguros pero más lentos
- [[Newton-Raphson]] — el mejor método de punto fijo: $g'(r) = 0$
- [[Metodos-Iterativos-Sistemas]] — la misma idea aplicada a sistemas lineales
