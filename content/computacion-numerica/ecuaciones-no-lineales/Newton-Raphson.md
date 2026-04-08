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

> [!note] Interpretación geométrica
> Se traza la **recta tangente** a $f$ en $x_0$. Esa recta corta al eje X en $x_1$. Se repite el proceso desde $x_1$: trazar la tangente, encontrar el corte → $x_2$, y así hasta que $|f(x_n)|$ sea suficientemente pequeño.

La tangente en $x_0$ es: $y = f(x_0) + f'(x_0)(x - x_0)$.

Su raíz (donde $y = 0$):

$$x_1 = x_0 - \frac{f(x_0)}{f'(x_0)}$$

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
