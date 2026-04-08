---
title: Bisección y Regula Falsi
description: "Métodos de intervalo para encontrar raíces de f(x)=0. Seguros y siempre convergentes, pero lentos."
date: 2026-04-08
type: topic
subject: Computación Numérica
sources: [t2-ecuaciones-no-lineales.md]
tags: [biseccion, regula-falsi, ecuaciones-no-lineales, raices, computacion-numerica, universidad]
---

# Bisección y Regula Falsi

## El problema: encontrar raíces

Queremos encontrar $r \in \mathbb{R}$ tal que $f(r) = 0$.

**¿Por qué métodos numéricos?** Porque en general no es posible "despejar" $x$ de la ecuación. Ejemplo: las cuotas de un préstamo de 18030.36€ a 120 meses con cuotas de 258.68€ verifican:

$$f(x) = 5.8085x \cdot \left(1 + \frac{x}{12}\right)^{-120} - 1 = 0$$

No existe ninguna fórmula para despejar el tipo de interés $x$.

### Teorema de Bolzano — base de los métodos de intervalo

> Si $f : [a, b] \to \mathbb{R}$ es continua y $f(a) \cdot f(b) < 0$ (signos opuestos), entonces existe al menos una raíz en $(a, b)$.

**Idea clave:** si $f$ cambia de signo en $[a, b]$, hay una raíz ahí. Los métodos de intervalo localizan esa raíz reduciendo el intervalo progresivamente.

---

## Método de Bisección

### Idea

Dividir el intervalo por la mitad en cada paso, quedándose con el subintervalo donde $f$ cambia de signo.

### Algoritmo

Dados $a_1 = a$, $b_1 = b$ con $f(a) \cdot f(b) < 0$:

$$x_n = \frac{a_n + b_n}{2} \quad \text{(punto medio)}$$

1. Si $f(x_n) = 0$ → $x_n$ es la raíz exacta.
2. Si $f(a_n) \cdot f(x_n) < 0$ → la raíz está en $[a_n, x_n]$: tomar $a_{n+1} = a_n$, $b_{n+1} = x_n$.
3. Si $f(a_n) \cdot f(x_n) > 0$ → la raíz está en $[x_n, b_n]$: tomar $a_{n+1} = x_n$, $b_{n+1} = b_n$.

### Cota del error

$$e_n = |x_n - r| \leq \frac{b - a}{2^n}$$

El intervalo se reduce a la mitad en cada iteración. Para conseguir $n$ cifras decimales de precisión, necesitamos al menos:

$$N \geq \frac{\log\left(\frac{b-a}{\varepsilon}\right)}{\log 2} \approx 3.32 \cdot \log_{10}\left(\frac{b-a}{\varepsilon}\right)$$

iteraciones, donde $\varepsilon$ es la precisión deseada.

> [!example] Ejemplo
> $f(x) = x^5 + x^3 + x - 3$, $[0, 3]$. Para 8 dígitos de precisión ($\varepsilon = 10^{-8}$):
> $$N \geq \frac{\log(3/10^{-8})}{\log 2} = \frac{\log(3 \times 10^8)}{\log 2} \approx 28.2 \implies N = 29 \text{ iteraciones}$$

### Propiedades

| Propiedad | Valor |
|-----------|-------|
| Convergencia | **Siempre** (si se cumplen las condiciones de Bolzano) |
| Velocidad | Lineal: orden 1 |
| Necesita $f'$ | No |
| Número de raíces | Encuentra **una** raíz (puede haber varias en el intervalo) |

> [!warning] Limitación
> Es lento. Si el intervalo inicial es $[0, 3]$, después de 10 iteraciones el error máximo es $3/2^{10} \approx 0.003$. Newton-Raphson consigue la misma precisión en 4-5 iteraciones.

---

## Método de Regula Falsi (Falsa Posición)

### Idea

Similar a bisección, pero en vez de tomar el punto medio, toma la **intersección con el eje X de la recta que une $(a_n, f(a_n))$ y $(b_n, f(b_n))$**.

### Fórmula

$$x_n = a_n - \frac{b_n - a_n}{f(b_n) - f(a_n)} \cdot f(a_n)$$

Que es equivalente a:

$$x_n = \frac{a_n \cdot f(b_n) - b_n \cdot f(a_n)}{f(b_n) - f(a_n)}$$

### Comparativa con bisección

| Método | Fórmula | Criterio |
|--------|---------|---------|
| **Bisección** | $x_n = \dfrac{a_n + b_n}{2}$ | Punto central geométrico |
| **Regula Falsi** | $x_n = \dfrac{a_n f(b_n) - b_n f(a_n)}{f(b_n) - f(a_n)}$ | Ponderado por los valores de $f$ |

<svg viewBox="0 0 500 220" width="500" height="220" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <line x1="60" y1="120" x2="455" y2="120" stroke="currentColor" stroke-width="1.2"/>
  <text x="458" y="124" font-size="11" fill="currentColor">x</text>
  <line x1="80" y1="115" x2="80" y2="125" stroke="currentColor" stroke-width="1.5"/>
  <text x="74" y="140" font-size="12" fill="currentColor">a</text>
  <line x1="420" y1="115" x2="420" y2="125" stroke="currentColor" stroke-width="1.5"/>
  <text x="415" y="140" font-size="12" fill="currentColor">b</text>
  <text x="52" y="175" font-size="11" fill="#c0582a">f(a)&lt;0</text>
  <text x="390" y="40" font-size="11" fill="#5ba85e">f(b)&gt;0</text>
  <path d="M 80,170 C 120,165 170,135 215,120 C 265,105 340,72 420,42" fill="none" stroke="#4a7fa5" stroke-width="2.5"/>
  <line x1="80" y1="170" x2="420" y2="42" stroke="#9b59b6" stroke-width="1.5" stroke-dasharray="5,3" opacity="0.8"/>
  <circle cx="80" cy="170" r="4" fill="#c0582a"/>
  <circle cx="420" cy="42" r="4" fill="#5ba85e"/>
  <circle cx="215" cy="120" r="6" fill="none" stroke="#4a7fa5" stroke-width="2"/>
  <text x="207" y="113" font-size="11" fill="#4a7fa5">r</text>
  <circle cx="250" cy="120" r="5" fill="#e07b39"/>
  <line x1="250" y1="115" x2="250" y2="125" stroke="#e07b39" stroke-width="1.5"/>
  <text x="242" y="142" font-size="11" fill="#e07b39">m_B</text>
  <circle cx="214" cy="120" r="5" fill="#9b59b6"/>
  <line x1="214" y1="115" x2="214" y2="125" stroke="#9b59b6" stroke-width="1.5"/>
  <text x="188" y="153" font-size="11" fill="#9b59b6">m_RF</text>
  <line x1="80" y1="170" x2="80" y2="120" stroke="currentColor" stroke-width="1" stroke-dasharray="3,3" opacity="0.3"/>
  <line x1="420" y1="42" x2="420" y2="120" stroke="currentColor" stroke-width="1" stroke-dasharray="3,3" opacity="0.3"/>
  <circle cx="72" cy="200" r="4" fill="#e07b39"/>
  <text x="80" y="204" font-size="11" fill="currentColor">Bisección: punto medio (a+b)/2</text>
  <circle cx="72" cy="216" r="4" fill="#9b59b6"/>
  <text x="80" y="220" font-size="11" fill="currentColor">Regula Falsi: intersección de la secante (más cerca de r)</text>
</svg>

Regula Falsi es generalmente **más rápido** que bisección porque usa información sobre el valor de $f$, no solo su signo. Sin embargo, en algunos casos uno de los extremos puede "quedarse fijo" muchas iteraciones, siendo bisección más fiable en la práctica.

---

## ¿Cuándo usar métodos de intervalo?

- Cuando necesitas **garantía de convergencia** (Newton puede divergir).
- Para obtener una **estimación inicial** buena que se pasa luego a Newton.
- Cuando no puedes o no quieres calcular la derivada $f'$.
- Bisección es el método de referencia para comparar con otros.

---

## Related Topics

- [[Punto-Fijo]] — alternativa cuando se tiene una buena $g(x)$
- [[Newton-Raphson]] — mucho más rápido, pero necesita $f'$ y una buena estimación inicial
- [[Aritmetica-Finita-y-Error]] — cómo el error de redondeo afecta el criterio de parada
