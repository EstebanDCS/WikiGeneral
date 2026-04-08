---
title: Integración Numérica
description: "Calcular ∫f(x)dx cuando no existe primitiva cerrada. Punto medio, trapecio, Simpson, fórmulas compuestas y cuadratura Gaussiana."
date: 2026-04-08
type: topic
subject: Computación Numérica
sources: [t5-integracion-numerica.md]
tags: [integracion-numerica, cuadratura, trapecio, simpson, gauss, computacion-numerica, universidad]
---

# Integración Numérica

## Motivación

Queremos calcular $\displaystyle\int_a^b f(x)\,dx$, pero:
- Solo conocemos $f$ en algunos puntos (datos experimentales).
- No existe una primitiva elemental de $f$ (ej: $e^{-x^2}$, $\sin(x)/x$).
- La primitiva existe pero es complicada de evaluar.

**Idea general:** aproximar $f$ por un polinomio interpolador $P_n$ y calcular $\int_a^b P_n(x)\,dx$ (esto sí tiene primitiva exacta).

$$\int_a^b f(x)\,dx \approx \int_a^b P_n(x)\,dx = \sum_{i=0}^{n} a_i f(x_i)$$

Estas fórmulas se llaman **reglas de cuadratura** o **fórmulas de cuadratura**.

---

## Fórmulas de Cuadratura Simples

Se aplican a intervalos $[a,b]$ completos con pocos nodos.

### Regla del Punto Medio ($n = 0$)

Aproximar $f$ por la constante $f\!\left(\frac{a+b}{2}\right)$:

$$\int_a^b f(x)\,dx \approx (b-a)\, f\!\left(\frac{a+b}{2}\right)$$

**Error de truncamiento:**

$$E = \frac{(b-a)^3}{24} f''(\xi), \quad \xi \in (a,b)$$

### Regla del Trapecio ($n = 1$)

Aproximar $f$ por la recta que une $(a, f(a))$ y $(b, f(b))$:

$$\int_a^b f(x)\,dx \approx \frac{b-a}{2}\bigl[f(a) + f(b)\bigr]$$

**Error de truncamiento:**

$$E = -\frac{(b-a)^3}{12} f''(\xi), \quad \xi \in (a,b)$$

### Regla de Simpson ($n = 2$)

Aproximar $f$ por la parábola que pasa por $a$, el punto medio $m = (a+b)/2$ y $b$. Con $h = (b-a)/2$:

$$\int_a^b f(x)\,dx \approx \frac{h}{3}\left[f(a) + 4f\!\left(\frac{a+b}{2}\right) + f(b)\right]$$

**Error de truncamiento:**

$$E = -\frac{(b-a)^5}{2880} f^{(4)}(\xi), \quad \xi \in (a,b)$$

> [!tip] Dato curioso
> Simpson es exacta para polinomios de grado $\leq 3$, aunque usa solo 3 nodos (lo que implicaría exactitud hasta grado 2). Este "regalo" es por la simetría de los nodos.

### Grado de exactitud

| Fórmula | Nodos | Grado de exactitud |
|---------|-------|-------------------|
| Punto Medio | 1 | 1 |
| Trapecio | 2 | 1 |
| Simpson | 3 | **3** |
| Simpson 3/8 | 4 | **3** |

El grado de exactitud es el mayor grado $n$ para el que la fórmula es **exacta** para todo polinomio de grado $\leq n$.

<svg viewBox="0 0 480 240" width="480" height="240" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <line x1="50" y1="215" x2="440" y2="215" stroke="currentColor" stroke-width="1.2"/>
  <line x1="50" y1="215" x2="50" y2="20" stroke="currentColor" stroke-width="1.2"/>
  <text x="443" y="219" font-size="11" fill="currentColor">x</text>
  <text x="145" y="228" font-size="11" fill="currentColor">a</text>
  <text x="323" y="228" font-size="11" fill="currentColor">b</text>
  <text x="231" y="228" font-size="11" fill="currentColor">m</text>
  <path d="M 60,155 C 90,138 120,112 150,100 C 190,86 220,89 240,96 C 270,106 300,133 330,146 C 360,162 395,188 420,204" fill="none" stroke="#4a7fa5" stroke-width="2.5"/>
  <text x="425" y="202" font-size="11" fill="#4a7fa5">f(x)</text>
  <polygon points="150,100 330,146 330,215 150,215" fill="#c0582a" opacity="0.15"/>
  <line x1="150" y1="100" x2="330" y2="146" stroke="#c0582a" stroke-width="1.8" stroke-dasharray="5,3"/>
  <path d="M 150,215 L 150,100 Q 240,69 330,146 L 330,215 Z" fill="#5ba85e" opacity="0.18"/>
  <path d="M 150,100 Q 240,69 330,146" fill="none" stroke="#5ba85e" stroke-width="2" stroke-dasharray="4,3"/>
  <line x1="150" y1="100" x2="150" y2="215" stroke="currentColor" stroke-width="1" opacity="0.35"/>
  <line x1="240" y1="96" x2="240" y2="215" stroke="currentColor" stroke-width="1" opacity="0.35" stroke-dasharray="3,2"/>
  <line x1="330" y1="146" x2="330" y2="215" stroke="currentColor" stroke-width="1" opacity="0.35"/>
  <circle cx="150" cy="100" r="3" fill="currentColor"/>
  <circle cx="240" cy="96" r="3" fill="currentColor"/>
  <circle cx="330" cy="146" r="3" fill="currentColor"/>
  <rect x="60" y="20" width="14" height="10" fill="#c0582a" opacity="0.4"/>
  <text x="78" y="30" font-size="11" fill="currentColor">Trapecio (recta entre extremos)</text>
  <rect x="60" y="36" width="14" height="10" fill="#5ba85e" opacity="0.5"/>
  <text x="78" y="46" font-size="11" fill="currentColor">Simpson (parábola por a, m, b) — más preciso</text>
</svg>

---

## Fórmulas Compuestas

### Problema de las fórmulas simples

Las fórmulas simples tienen error proporcional a potencias de $(b-a)$. Si el intervalo es grande, el error puede ser enorme.

**Solución:** dividir $[a,b]$ en $n$ subintervalos de anchura $h = (b-a)/n$ y aplicar la fórmula simple en cada uno.

### Trapecio Compuesto

Con nodos $x_j = a + jh$, $j = 0, 1, \ldots, n$:

$$\int_a^b f(x)\,dx \approx \frac{h}{2}\left[f(a) + 2\sum_{j=1}^{n-1} f(x_j) + f(b)\right]$$

**Error:**

$$E = -\frac{(b-a)h^2}{12} f''(\xi) \quad \Rightarrow \quad |E| \leq \frac{(b-a)h^2}{12} M_2$$

El error es $\mathcal{O}(h^2)$: duplicar el número de subintervalos divide el error por 4.

### Simpson Compuesto

Se necesita $n = 2m$ (número par de subintervalos). Con $h = (b-a)/(2m)$:

$$\int_a^b f(x)\,dx \approx \frac{h}{3}\left[f(a) + 2\sum_{j=1}^{m-1} f(x_{2j}) + 4\sum_{j=1}^{m} f(x_{2j-1}) + f(b)\right]$$

**Error:**

$$E = -\frac{(b-a)h^4}{180} f^{(4)}(\xi) \quad \Rightarrow \quad |E| \leq \frac{(b-a)h^4}{180} M_4$$

El error es $\mathcal{O}(h^4)$: duplicar el número de subintervalos divide el error por **16**.

> [!example] Comparativa para $\int_0^2 (e^x - 3x^2)\,dx$ con $h = 0.5$
>
> | Método | Error real | Cota teórica |
> |--------|-----------|-------------|
> | Trapecio compuesto | $1.17 \times 10^{-1}$ | $2.08 \times 10^{-1}$ |
> | Simpson compuesto | $2.15 \times 10^{-3}$ | $5.13 \times 10^{-3}$ |
>
> Simpson es ~50 veces más preciso con los mismos nodos.

---

## Cuadratura Gaussiana

### La idea clave

Las fórmulas de Newton-Cotes (trapecio, Simpson...) usan nodos **equiespaciados**. ¿Podemos elegir los nodos de forma óptima para maximizar la precisión?

**Sí.** Con $n$ nodos bien elegidos se puede alcanzar exactitud para polinomios de grado $\leq 2n-1$ (en vez de $\leq n$ con nodos equiespaciados).

### Los nodos de Gauss-Legendre

Los nodos óptimos son las raíces del **polinomio de Legendre** $L_n(x)$ en $[-1, 1]$:

$$L_0(x) = 1, \quad L_1(x) = x, \quad L_n(x) = \frac{2n-1}{n} x L_{n-1}(x) - \frac{n-1}{n} L_{n-2}(x)$$

| $n$ | Nodos $r_i$ | Coeficientes $c_i$ |
|-----|------------|-------------------|
| 1 | $0$ | $2$ |
| 2 | $\pm 1/\sqrt{3}$ | $1, 1$ |
| 3 | $0$, $\pm\sqrt{3/5}$ | $8/9$, $5/9$, $5/9$ |

La fórmula en $[-1, 1]$:

$$\int_{-1}^{1} f(x)\,dx \approx \sum_{i=1}^{n} c_i f(r_i)$$

### Extensión a $[a,b]$

Cambio de variable $x = \frac{b-a}{2}t + \frac{a+b}{2}$, con $t \in [-1, 1]$:

$$\int_a^b f(x)\,dx = \frac{b-a}{2} \int_{-1}^{1} f\!\left(\frac{b-a}{2}t + \frac{a+b}{2}\right)dt$$

Se aplica cuadratura Gaussiana a la integral del lado derecho.

**Teorema:** con $n$ nodos gaussianos, la fórmula es exacta para todo polinomio de grado $\leq 2n-1$.

> [!tip] Eficiencia
> Con solo **3 nodos de Gauss** se integra exactamente cualquier polinomio de grado $\leq 5$. Con **trapecio compuesto** se necesitarían muchos más subintervalos para lograr la misma precisión.

---

## Comparativa final

| Método | Nodos | Exactitud (polinomios) | Error |
|--------|-------|----------------------|-------|
| Punto Medio | 1 | grado ≤ 1 | $\mathcal{O}(h^3)$ |
| Trapecio | 2 | grado ≤ 1 | $\mathcal{O}(h^3)$ |
| Simpson | 3 | grado ≤ 3 | $\mathcal{O}(h^5)$ |
| Trapecio compuesto ($n$ nodos) | $n$ | grado ≤ 1 | $\mathcal{O}(h^2)$ |
| Simpson compuesto ($n$ nodos) | $n$ | grado ≤ 3 | $\mathcal{O}(h^4)$ |
| Gauss ($n$ nodos) | $n$ | grado ≤ $2n-1$ | Exponencial en $n$ |

---

## Related Topics

- [[Interpolacion-Polinomial]] — las fórmulas de cuadratura se derivan interpolando $f$
- [[Aritmetica-Finita-y-Error]] — el error de truncamiento se acompaña del error de redondeo
