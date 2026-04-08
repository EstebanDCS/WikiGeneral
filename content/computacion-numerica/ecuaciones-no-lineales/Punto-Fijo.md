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

<svg viewBox="0 0 300 300" width="300" height="300" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <line x1="40" y1="280" x2="40" y2="20" stroke="currentColor" stroke-width="1.2"/>
  <line x1="40" y1="280" x2="285" y2="280" stroke="currentColor" stroke-width="1.2"/>
  <text x="287" y="284" font-size="11" fill="currentColor">x</text>
  <text x="44" y="18" font-size="11" fill="currentColor">y</text>
  <line x1="40" y1="280" x2="280" y2="40" stroke="currentColor" stroke-width="1.2" stroke-dasharray="4,3" opacity="0.5"/>
  <text x="268" y="36" font-size="11" fill="currentColor">y=x</text>
  <line x1="40" y1="220" x2="280" y2="100" stroke="#4a7fa5" stroke-width="2.5"/>
  <text x="283" y="98" font-size="11" fill="#4a7fa5">g(x)</text>
  <circle cx="160" cy="160" r="6" fill="none" stroke="#c0582a" stroke-width="2.5"/>
  <text x="164" y="152" font-size="10" fill="#c0582a">r</text>
  <polyline points="64,280 64,208 112,208 112,184 136,184 136,172 148,172 148,166 154,166 154,163 157,163 157,161 160,160" fill="none" stroke="#5ba85e" stroke-width="1.8" stroke-linejoin="miter"/>
  <line x1="64" y1="278" x2="64" y2="282" stroke="currentColor" stroke-width="1"/>
  <text x="57" y="294" font-size="11" fill="#5ba85e">x₀</text>
  <line x1="112" y1="278" x2="112" y2="282" stroke="currentColor" stroke-width="1"/>
  <text x="105" y="294" font-size="11" fill="#5ba85e">x₁</text>
  <line x1="136" y1="278" x2="136" y2="282" stroke="currentColor" stroke-width="1"/>
  <text x="129" y="294" font-size="11" fill="#5ba85e">x₂</text>
  <text x="168" y="294" font-size="10" fill="#5ba85e">→ r</text>
</svg>

Cuando $|g'(r)| < 1$: la escalera converge al punto fijo. Cuando $|g'(r)| > 1$: la sucesión diverge.

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
