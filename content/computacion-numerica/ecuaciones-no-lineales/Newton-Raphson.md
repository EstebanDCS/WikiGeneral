---
title: Método de Newton-Raphson
description: "El método más potente para encontrar raíces: usa la tangente a la curva. Convergencia cuadrática cuando funciona."
date: 2026-04-08
type: topic
subject: Computación Numérica
sources: [t2-ecuaciones-no-lineales.md]
tags: [newton-raphson, convergencia-cuadratica, secante, computacion-numerica, universidad]
---

# Método de Newton-Raphson

## Idea

Tenemos $f(x) = 0$ y una aproximación $x_0$ de la raíz $r$. En vez de trabajar con $f$, la **aproximamos por su recta tangente** en $x_0$, que es mucho más sencilla. La raíz de la tangente es la siguiente aproximación.

La tangente en $x_0$ es: $y = f(x_0) + f'(x_0)(x - x_0)$.

Su raíz (donde $y = 0$):

$$x_1 = x_0 - \frac{f(x_0)}{f'(x_0)}$$

<svg viewBox="0 0 520 265" width="520" height="265" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <line x1="55" y1="15" x2="55" y2="240" stroke="currentColor" stroke-width="1.2"/>
  <line x1="50" y1="141" x2="470" y2="141" stroke="currentColor" stroke-width="1.2"/>
  <text x="473" y="145" font-size="12" fill="currentColor">x</text>
  <text x="60" y="13" font-size="12" fill="currentColor">y</text>
  <polyline points="60,212 81,202 102,193 123,183 144,170 165,158 189,141 207,128 227,112 249,95 270,77 291,58 312,37 333,16" fill="none" stroke="#4a7fa5" stroke-width="2.5"/>
  <text x="338" y="14" font-size="12" fill="#4a7fa5">f(x)</text>
  <line x1="155" y1="192" x2="330" y2="19" stroke="#c0582a" stroke-width="1.8" stroke-dasharray="6,3"/>
  <line x1="102" y1="206" x2="291" y2="66" stroke="#5ba85e" stroke-width="1.8" stroke-dasharray="6,3"/>
  <circle cx="312" cy="37" r="5" fill="#c0582a"/>
  <circle cx="207" cy="128" r="5" fill="#5ba85e"/>
  <circle cx="207" cy="141" r="4" fill="#c0582a"/>
  <circle cx="190" cy="141" r="4" fill="#5ba85e"/>
  <circle cx="190" cy="141" r="7" fill="none" stroke="#4a7fa5" stroke-width="2"/>
  <line x1="312" y1="37" x2="312" y2="141" stroke="#c0582a" stroke-width="1" stroke-dasharray="3,3" opacity="0.5"/>
  <line x1="207" y1="128" x2="207" y2="141" stroke="#5ba85e" stroke-width="1" stroke-dasharray="3,3" opacity="0.5"/>
  <text x="297" y="158" font-size="12" fill="#c0582a">x₀</text>
  <text x="200" y="158" font-size="12" fill="#c0582a">x₁</text>
  <text x="164" y="158" font-size="12" fill="#5ba85e">x₂≈r</text>
  <line x1="60" y1="256" x2="90" y2="256" stroke="#c0582a" stroke-width="1.8" stroke-dasharray="6,3"/>
  <text x="94" y="260" font-size="11" fill="currentColor">tangente en x₀ → x₁</text>
  <line x1="255" y1="256" x2="285" y2="256" stroke="#5ba85e" stroke-width="1.8" stroke-dasharray="6,3"/>
  <text x="289" y="260" font-size="11" fill="currentColor">tangente en x₁ → x₂</text>
</svg>

---

## Fórmula iterativa

$$\boxed{x_{n+1} = x_n - \frac{f(x_n)}{f'(x_n)}}$$

Es un [[Punto-Fijo|método de punto fijo]] para $g(x) = x - \dfrac{f(x)}{f'(x)}$.

**Condición:** $f'(x_n) \neq 0$ en cada iteración.

---

## Teorema de Convergencia Local

> Sean $f \in C^2([a,b])$ y $r$ raíz de $f$ con $f'(r) \neq 0$. Entonces existe $\delta > 0$ tal que si $x_0 \in (r-\delta, r+\delta)$, la sucesión de Newton converge a $r$ con **convergencia cuadrática**:
> $$\lim_{n \to \infty} \frac{e_{n+1}}{e_n^2} = \frac{|f''(r)|}{2|f'(r)|}$$

**¿Qué significa "orden 2"?** Si en un paso el error es $10^{-4}$, en el siguiente es aproximadamente $10^{-8}$. El número de cifras significativas correctas **se duplica en cada iteración**.

> [!example] Comparativa de velocidad
> Raíz de $f(x) = x^3 + 4x^2 - 10$ en $[1, 2]$, partiendo de $x_0 = 1.5$:
>
> | Método | Iteraciones para 10 dígitos |
> |--------|---------------------------|
> | Punto fijo (genérico) | ~30 |
> | Bisección | ~34 |
> | **Newton-Raphson** | **4** |

---

## Limitaciones y cuándo falla

| Problema | Causa | Solución |
|---------|-------|----------|
| $f'(x_n) = 0$ | La tangente es horizontal → no corta el eje | Cambiar $x_0$ o usar otro método |
| Raíz múltiple: $f(r) = f'(r) = 0$ | La convergencia ya no es cuadrática (es lineal) | Usar Newton para raíces múltiples |
| $x_0$ lejos de $r$ | Puede divergir o converger a otra raíz | Combinar con bisección para estimar $x_0$ |

> [!warning] Newton es local
> Solo garantiza convergencia si $x_0$ está suficientemente cerca de $r$. En la práctica, se combina bisección (para obtener un buen $x_0$) con Newton (para converger rápido).

---

## Variantes

### Método de la Secante

Sustituye $f'(x_n)$ por una aproximación usando dos puntos previos:

$$f'(x_n) \approx \frac{f(x_n) - f(x_{n-1})}{x_n - x_{n-1}}$$

$$\Rightarrow \quad x_{n+1} = x_n - f(x_n) \cdot \frac{x_n - x_{n-1}}{f(x_n) - f(x_{n-1})}$$

**Ventaja:** no necesita calcular $f'$, solo evaluar $f$ dos veces.  
**Convergencia:** orden $\varphi = \frac{1+\sqrt{5}}{2} \approx 1.618$ (el número áureo). Más lento que Newton, más rápido que bisección.

Necesita **dos puntos iniciales** $x_0$ y $x_1$.

### Método de Whittaker (Quasi-Newton)

Sustituye $f'(x_n)$ por un valor constante $f'(x_0)$ en todas las iteraciones:

$$x_{n+1} = x_n - \frac{f(x_n)}{f'(x_0)}$$

**Ventaja:** evita calcular $f'$ en cada paso.  
**Convergencia:** lineal (más lenta que Newton).

### Newton para Raíces Múltiples

Si $r$ es raíz de orden $k$ (es decir, $f(r) = f'(r) = \cdots = f^{(k)}(r) = 0$ pero $f^{(k+1)}(r) \neq 0$), la convergencia de Newton es solo **lineal**.

Solución: aplicar Newton a la función $h(x) = \dfrac{f(x)}{f'(x)}$, que tiene un **cero simple** en $r$.

---

## Resumen de todos los métodos para $f(x) = 0$

| Método | Orden | Necesita $f'$ | Necesita intervalo | Garantía |
|--------|-------|---------------|-------------------|----------|
| Bisección | 1 (lento) | No | Sí | Siempre converge |
| Regula Falsi | 1 | No | Sí | Siempre converge |
| Punto fijo | 1 | No (solo $g$) | No | Si $|g'(r)| < 1$ |
| **Newton-Raphson** | **2** | **Sí** | No | Local |
| Secante | 1.618 | No | No | Local |

---

## Related Topics

- [[Punto-Fijo]] — Newton como caso especial con $g'(r) = 0$
- [[Biseccion-y-Regula-Falsi]] — métodos más seguros para estimar el punto de partida
- [[Ecuaciones-Algebraicas]] — algoritmo de Horner para evaluar $f$ y $f'$ eficientemente en polinomios
- [[Sistemas-No-Lineales]] — extensión de Newton a sistemas de ecuaciones
