---
title: Guía Examen Parcial 2 — Temas 4–6
type: output
output_type: study-guide
date: 2026-04-24
subject: Computación Numérica
based_on: [Tema5-Integracion.pdf, Tema6-Optimizacion.pdf, Prb_IntegraNumerica.pdf, Interpolacion-Polinomial.md, Ajuste-de-Datos.md, Integracion-Numerica.md]
tags: [computacion-numerica, examen, parcial2, interpolacion, integracion, optimizacion]
---

# Guía Examen Parcial 2 — Temas 4–6

> **Contexto**: El Parcial 2 cubre Temas 4 (interpolación y ajuste), 5 (integración numérica) y 6 (optimización). Como suspendiste el Parcial 1, en este examen **también entran los Temas 1–3** — consulta la [[Examen-Parcial-1-Guia]] para esos temas.

---

## TEMA 4 — Interpolación Polinomial y Ajuste de Datos

### 4.1 Problema de interpolación

**Dado**: $n+1$ nodos $(x_0, y_0), (x_1, y_1), \ldots, (x_n, y_n)$ con $x_i$ distintos.  
**Encontrar**: Polinomio $P_n(x)$ de grado $\leq n$ tal que $P_n(x_i) = y_i$ para todo $i$.

**Teorema de unicidad**: existe un único polinomio interpolante de grado $\leq n$ para $n+1$ puntos.

### 4.2 Polinomio de Lagrange

$$P_n(x) = \sum_{i=0}^{n} y_i \cdot L_i(x), \qquad L_i(x) = \prod_{j=0, j\neq i}^{n} \frac{x - x_j}{x_i - x_j}$$

**Ejemplo**: 3 puntos $(0, 1), (1, 3), (2, 2)$:
$$L_0 = \frac{(x-1)(x-2)}{(0-1)(0-2)} = \frac{(x-1)(x-2)}{2}$$
$$L_1 = \frac{x(x-2)}{1 \cdot (-1)} = -x(x-2)$$
$$L_2 = \frac{x(x-1)}{2 \cdot 1} = \frac{x(x-1)}{2}$$
$$P_2(x) = \frac{(x-1)(x-2)}{2} + 3\cdot(-x(x-2)) + 2\cdot\frac{x(x-1)}{2}$$
$$= \frac{x^2-3x+2}{2} - 3x^2+6x + x^2-x = -\frac{3}{2}x^2 + \frac{5}{2}x + 1$$

### 4.3 Diferencias divididas de Newton

Define una tabla de diferencias divididas:
$$f[x_i] = f(x_i)$$
$$f[x_i, x_{i+1}] = \frac{f[x_{i+1}] - f[x_i]}{x_{i+1} - x_i}$$
$$f[x_i, x_{i+1}, x_{i+2}] = \frac{f[x_{i+1}, x_{i+2}] - f[x_i, x_{i+1}]}{x_{i+2} - x_i}$$

**Polinomio de Newton**:
$$P_n(x) = f[x_0] + f[x_0,x_1](x-x_0) + f[x_0,x_1,x_2](x-x_0)(x-x_1) + \cdots$$

**Ventaja**: añadir un nodo nuevo es fácil (solo añadir un término).

### 4.4 Nodos de Chebyshev (minimizar el error)

Para interpolación en $[a,b]$ con $n+1$ nodos:
$$x_k = \frac{a+b}{2} + \frac{b-a}{2}\cos\left(\frac{(2k-1)\pi}{2(n+1)}\right), \quad k = 1, \ldots, n+1$$

**Por qué**: minimizan $\max_{x\in[a,b]}|\omega(x)|$ donde $\omega(x) = \prod(x-x_i)$.

**Error de interpolación con Chebyshev**:
$$|f(x) - P_n(x)| \leq \frac{\max|f^{(n+1)}|}{(n+1)!} \cdot \frac{(b-a)^{n+1}}{2^{2n+1}}$$

Vs. nodos equiespaciados: $\frac{h^{n+1}}{4(n+1)}$ donde $h = (b-a)/n$ — Chebyshev es **mucho mejor para $n$ grande** (evita fenómeno de Runge).

### 4.5 Splines cúbicos

**Problema**: los polinomios de alto grado oscilan mucho (Runge). Los splines usan polinomios cúbicos por tramos con continuidad de derivadas.

**Spline cúbico natural**: $S''(x_0) = S''(x_n) = 0$

Cada tramo $[x_i, x_{i+1}]$: $S_i(x) = a_i + b_i(x-x_i) + c_i(x-x_i)^2 + d_i(x-x_i)^3$

Con condiciones: $S_i(x_i)=y_i$, $S_i(x_{i+1})=y_{i+1}$, $S_i'(x_{i+1})=S_{i+1}'(x_{i+1})$, $S_i''(x_{i+1})=S_{i+1}''(x_{i+1})$

### 4.6 Mínimos cuadrados (ajuste, no interpolación)

Cuando hay más puntos que grados del polinomio y queremos minimizar el error cuadrático:

$$\min_{a_0,\ldots,a_m} \sum_{i=0}^{n} \left(y_i - \sum_{j=0}^{m} a_j x_i^j\right)^2$$

**Sistema normal** (ecuaciones de Gauss):
$$\left(\sum x_i^{j+k}\right) a_k = \sum x_i^j y_i \quad \Rightarrow \quad \mathbf{A}^T\mathbf{A}\,\mathbf{a} = \mathbf{A}^T\mathbf{b}$$

Para **ajuste lineal** ($y = \alpha x + \beta$):
$$\alpha = \frac{n\sum x_i y_i - (\sum x_i)(\sum y_i)}{n\sum x_i^2 - (\sum x_i)^2}, \quad \beta = \bar{y} - \alpha\bar{x}$$

### 4.7 Transformada Discreta de Fourier (DFT)

Para señal periódica muestreada en $n$ puntos $y_0, y_1, \ldots, y_{n-1}$:
$$c_k = \frac{1}{n}\sum_{j=0}^{n-1} y_j e^{-2\pi i jk/n}, \quad k = 0, 1, \ldots, n-1$$

Reconstrucción: $y_j = \sum_{k=0}^{n-1} c_k e^{2\pi i jk/n}$

### 4.8 Error de interpolación

Para el polinomio interpolante de grado $n$:
$$f(x) - P_n(x) = \frac{f^{(n+1)}(\xi)}{(n+1)!}\prod_{i=0}^{n}(x-x_i)$$

donde $\xi \in [\min x_i, \max x_i]$.

**Cota del error**:
$$|f(x) - P_n(x)| \leq \frac{M_{n+1}}{(n+1)!}\max_{x\in[a,b]}\left|\prod_{i=0}^{n}(x-x_i)\right|$$

donde $M_{n+1} = \max|f^{(n+1)}(x)|$.

---

## TEMA 5 — Integración Numérica

### 5.1 Reglas simples de Newton-Cotes

Para $\int_a^b f(x)dx$ con $h = b-a$:

| Regla | Fórmula | Error | Grado exactitud |
|-------|---------|-------|-----------------|
| **Punto medio** | $h \cdot f\left(\frac{a+b}{2}\right)$ | $\leq \frac{M_2 h^3}{24}$ | 1 |
| **Trapecio** | $\frac{h}{2}[f(a)+f(b)]$ | $\leq \frac{M_2 h^3}{12}$ | 1 |
| **Simpson 1/3** | $\frac{h}{6}[f(a)+4f(m)+f(b)]$, $m=(a+b)/2$ | $\leq \frac{M_4 h^5}{2880}$ | **3** |
| **Simpson 3/8** | $\frac{h}{8}[f(a)+3f(c)+3f(d)+f(b)]$, 3 subint. | $\leq \frac{M_4 h^5}{6480}$ | **3** |

donde $M_k = \max_{x\in[a,b]}|f^{(k)}(x)|$ y $h = b-a$ (en las reglas simples).

> **Grado de exactitud**: número $m$ tal que la regla es exacta para todos los polinomios de grado $\leq m$ pero no para $x^{m+1}$.

### 5.2 Fórmulas compuestas

Dividir $[a,b]$ en $n$ subintervalos iguales de anchura $h = (b-a)/n$:

**Trapecio compuesto** (con $x_i = a + ih$):
$$T_n = \frac{h}{2}\left[f(x_0) + 2\sum_{i=1}^{n-1}f(x_i) + f(x_n)\right]$$
$$|E| \leq \frac{(b-a)M_2 h^2}{12} = \frac{(b-a)^3 M_2}{12n^2} \quad (O(h^2))$$

**Simpson compuesto** ($n$ par, $h = (b-a)/(2m)$, $2m$ subintervalos):
$$S_m = \frac{h}{3}\left[f(x_0) + 4f(x_1) + 2f(x_2) + 4f(x_3) + \cdots + 4f(x_{2m-1}) + f(x_{2m})\right]$$
$$|E| \leq \frac{(b-a)M_4 h^4}{180} \quad (O(h^4))$$

> **Regla mnemotécnica Simpson**: 1, 4, 2, 4, 2, ..., 4, 1

**Para hallar $n$ que da un error máximo $\epsilon$**:
- Trapecio: $n \geq \sqrt{\frac{(b-a)^3 M_2}{12\epsilon}}$
- Simpson: $n \geq \left(\frac{(b-a)^5 M_4}{180\epsilon}\right)^{1/4}$

### 5.3 Cuadratura Gaussiana

**Idea**: elegir los puntos de evaluación óptimamente (no equiespaciados) para maximizar el grado de exactitud.

Con **$n$ nodos**: grado de exactitud = $2n - 1$.

**Cuadratura de Gauss-Legendre** en $[-1, 1]$:
$$\int_{-1}^{1} f(t)\, dt \approx \sum_{k=1}^{n} w_k f(t_k)$$

| n | Nodos $t_k$ | Pesos $w_k$ |
|---|------------|-------------|
| 1 | 0 | 2 |
| 2 | $\pm 1/\sqrt{3} \approx \pm 0.5774$ | 1, 1 |
| 3 | 0, $\pm\sqrt{3/5} \approx \pm 0.7746$ | 8/9, 5/9, 5/9 |
| 4 | $\pm 0.3399$, $\pm 0.8611$ | 0.6521, 0.6521, 0.3479, 0.3479 |
| 5 | 0, $\pm 0.5385$, $\pm 0.9062$ | 0.5689, 0.4786, 0.4786, 0.2369, 0.2369 |

**Cambio de variable para $[a, b]$**:
$$\int_a^b f(x)\,dx = \frac{b-a}{2}\int_{-1}^{1} f\!\left(\frac{(b-a)t + (a+b)}{2}\right)dt$$

### 5.4 Ejercicio tipo con Gauss-Legendre

**Ejemplo**: calcular $\int_0^1 e^{-x^2} dx$ con 3 nodos.

1. Cambio de variable: $x = \frac{(1-0)t + (0+1)}{2} = \frac{t+1}{2}$, $dx = \frac{dt}{2}$
2. $\int_0^1 e^{-x^2}dx = \frac{1}{2}\int_{-1}^{1} e^{-(t+1)^2/4}\,dt$
3. Con $n=3$: nodos $t_1=0$, $t_2=\sqrt{3/5}$, $t_3=-\sqrt{3/5}$; pesos $w_1=8/9$, $w_2=w_3=5/9$

$$\approx \frac{1}{2}\left[\frac{8}{9}e^{-1/4} + \frac{5}{9}e^{-(1+\sqrt{3/5})^2/4} + \frac{5}{9}e^{-(1-\sqrt{3/5})^2/4}\right]$$

### 5.5 Ejercicios reales (Prb_IntegraNumerica.pdf)

**Problema 1 (deducción de error de Simpson)**:
Por Taylor: $f(x) = f(m) + f'(m)(x-m) + \frac{f''(m)}{2}(x-m)^2 + \frac{f'''(m)}{6}(x-m)^3 + \frac{f^{(4)}(m)}{24}(x-m)^4 + \cdots$

Integrar en $[a,b]$ con $m = (a+b)/2$, $h = (b-a)/2$:
- Los términos de potencia impar se anulan por simetría
- $\int_{-h}^{h}(x-m)^2\,dx = \frac{2h^3}{3}$, $\int_{-h}^{h}(x-m)^4\,dx = \frac{2h^5}{5}$

Resultado: $\int_a^b f\,dx = 2hf(m) + \frac{h^3}{3}f''(m) + \frac{h^5}{60}f^{(4)}(m) + \cdots$

Simpson usa $f(a) + 4f(m) + f(b)$... al sustituir las expansiones de Taylor en $f(a)$ y $f(b)$ y simplificar:
$$\text{Error} = -\frac{h^5}{90}f^{(4)}(\xi) = -\frac{(b-a)^5}{2880}f^{(4)}(\xi)$$

**Problema con reglas compuestas** (ejemplo tipo):
$\int_0^1 \sin(\pi x)\,dx = \frac{2}{\pi} \approx 0.6366$

Con $n=4$ (trapecio), $h=0.25$, nodos: $0, 0.25, 0.5, 0.75, 1$:
$$T_4 = \frac{0.25}{2}[\sin(0) + 2\sin(\pi/4) + 2\sin(\pi/2) + 2\sin(3\pi/4) + \sin(\pi)]$$
$$= 0.125[0 + \sqrt{2} + 2 + \sqrt{2} + 0] = 0.125[2 + 2\sqrt{2}] \approx 0.6036$$

Error $\approx 0.033$; con Simpson el error sería $O(h^4)$, mucho menor.

### 5.6 Esquema para elegir método de integración

```
¿Cuántos puntos tengo?
    Solo los extremos a,b → Trapecio simple
    Un punto interior (el medio) → Simpson simple
    Puedo elegir los puntos → Gauss-Legendre (máxima precisión)

¿Cuánta precisión necesito?
    Error O(h²) tolerable → Trapecio compuesto
    Error O(h⁴) → Simpson compuesto
    Máxima precisión con pocos puntos → Cuadratura Gaussiana

¿Me piden calcular n para un error dado?
    Despejar n de la fórmula de error:
    Trapecio: n ≥ sqrt((b-a)³M₂/(12ε))
    Simpson: n ≥ ((b-a)⁵M₄/(180ε))^(1/4)
```

---

## TEMA 6 — Optimización

### 6.1 Condiciones de optimalidad

**Problema**: $\min f(x)$ con $f: \mathbb{R}^n \to \mathbb{R}$.

**Condición necesaria de primer orden**: si $x^*$ es mínimo local y $f \in C^1$:
$$\nabla f(x^*) = 0 \quad \text{(punto crítico)}$$

**Condición suficiente de segundo orden**: si $\nabla f(x^*) = 0$ y $H_f(x^*)$ es **definida positiva**:
$$x^* \text{ es mínimo local estricto}$$

**Matriz Hessiana**:
$$H_{ij} = \frac{\partial^2 f}{\partial x_i \partial x_j}$$

**Hessiano definido positivo**: todos los menores principales $> 0$ (criterio de Sylvester), equivalente a que todos los autovalores sean positivos.

### 6.2 Optimización en 1D — Sección Áurea

**Para minimizar $f$ en $[a, b]$ sin usar derivadas**:

Razón áurea: $\Phi = \frac{\sqrt{5}+1}{2} \approx 1.618$, $\frac{1}{\Phi} = \Phi - 1 \approx 0.618$

**Algoritmo**:
1. $\Delta = (\Phi - 1)(b - a) = \frac{b-a}{\Phi}$
2. $\alpha = b - \Delta$, $\beta = a + \Delta$
3. Si $f(\alpha) < f(\beta)$: el mínimo está en $[a, \beta]$ → nuevo $b = \beta$
4. Si $f(\alpha) \geq f(\beta)$: el mínimo está en $[\alpha, b]$ → nuevo $a = \alpha$
5. Repetir hasta $|b-a| < \epsilon$

**Contracción por iteración**: el intervalo se reduce por un factor $1/\Phi \approx 0.618$.

**Número de iteraciones para precisión $\epsilon$** a partir de intervalo $[a,b]$:
$$N \geq \frac{\ln\frac{b-a}{\epsilon}}{\ln \Phi} \approx \frac{\ln\frac{b-a}{\epsilon}}{0.4812}$$

**Ventaja de la sección áurea**: en cada paso, se reutiliza uno de los dos puntos evaluados (solo se necesita calcular **1 valor nuevo** de $f$ por iteración, no 2).

**Ejemplo**: minimizar $f(x) = x^2 - 2x + 3$ en $[0, 2]$ (mínimo exacto en $x=1$):
- $\Delta = (2-0)/\Phi = 2/1.618 \approx 1.236$
- $\alpha = 2 - 1.236 = 0.764$, $\beta = 0 + 1.236 = 1.236$
- $f(0.764) = 0.584-1.528+3 = 2.056$
- $f(1.236) = 1.528-2.472+3 = 2.056$
- Empate → cualquier subintervalo (en este caso el mínimo está en el interior)

### 6.3 Método de máxima pendiente (Gradient Descent)

**Para minimizar $f: \mathbb{R}^n \to \mathbb{R}$**:
$$x^{(k+1)} = x^{(k)} - \alpha_k \nabla f(x^{(k)})$$

donde $\alpha_k > 0$ es el tamaño de paso (puede ser fijo o determinado por búsqueda lineal).

**Elección del paso $\alpha_k$**: búsqueda en la dirección $d^{(k)} = -\nabla f(x^{(k)})$:
$$\alpha_k = \arg\min_{\alpha > 0} f(x^{(k)} + \alpha d^{(k)})$$

**Convergencia**: lenta cerca del mínimo (oscila si la función tiene "valles" alargados).

### 6.4 Gradiente Conjugado de Fletcher-Reeves

**Mejora el gradient descent** eligiendo direcciones conjugadas:

$$x^{(k+1)} = x^{(k)} + \alpha_k d^{(k)}$$
$$d^{(0)} = -\nabla f(x^{(0)})$$
$$d^{(k)} = -\nabla f(x^{(k)}) + \beta_k d^{(k-1)}$$
$$\beta_k = \frac{\|\nabla f(x^{(k)})\|^2}{\|\nabla f(x^{(k-1)})\|^2}$$

El paso $\alpha_k$ se elige por búsqueda lineal (exact line search).

**Para funciones cuadráticas $f(x) = \frac{1}{2}x^TAx - b^Tx$**: converge en exactamente $n$ pasos.

### 6.5 Programación Lineal — Método Símplex

**Forma estándar**:
$$\min c^T x \quad \text{s.t.} \quad Ax = b, \quad x \geq 0$$

**Forma aumentada** (convertir $\leq$ a $=$):
- $a^T x \leq b_i$ → $a^T x + s_i = b_i$, $s_i \geq 0$ (variable de holgura)
- $a^T x \geq b_i$ → $a^T x - s_i = b_i$, $s_i \geq 0$ (variable de exceso)

**Algoritmo Símplex tabular**:

1. **Tabla inicial**: construir con variables básicas (holguras) y no básicas
2. **Variable entrante**: columna con coeficiente de $c$ más negativo (criterio de reducción de coste)
3. **Variable saliente**: fila con menor ratio $b_i / a_{ij}$ (regla del cociente mínimo, $a_{ij} > 0$)
4. **Pivoteo**: hacer el elemento pivote = 1 y el resto de la columna = 0
5. **Terminar** cuando todos los coeficientes de $c$ son $\geq 0$ (mínimo alcanzado)

**Variables artificiales** (para $\geq$ o $=$):
- Añadir variable artificial $a_i \geq 0$ para obtener solución básica factible inicial
- Método de la $M$ grande: penalizar $a_i$ con $+M \cdot a_i$ en la función objetivo ($M$ muy grande)
- Si la solución óptima tiene alguna $a_i > 0$ → el problema es infactible

**Ejemplo** (del Tema 6):
$$\min z = -5x_1 - 4x_2 - 3x_3$$
$$\text{s.t.} \quad 6x_1 + 4x_2 + 2x_3 \leq 240$$
$$3x_1 + 2x_2 + 5x_3 \leq 270$$
$$5x_1 + 6x_2 + 5x_3 \leq 420$$
$$x_1, x_2, x_3 \geq 0$$

Añadir holguras $s_1, s_2, s_3$. Tabla inicial con base $\{s_1, s_2, s_3\}$...

### 6.6 Optimización con restricciones (Multiplicadores de Lagrange)

Para $\min f(x)$ sujeto a $g(x) = 0$:

**Condición necesaria**: $\nabla f(x^*) = \lambda \nabla g(x^*)$ (los gradientes son paralelos)

**Función de Lagrange**: $\mathcal{L}(x, \lambda) = f(x) - \lambda g(x)$

$$\nabla_x \mathcal{L} = 0 \quad \Rightarrow \quad \nabla f = \lambda \nabla g$$
$$\nabla_\lambda \mathcal{L} = 0 \quad \Rightarrow \quad g(x) = 0$$

**En Maxima** (Sesión 6):
```maxima
/* Minimizar f(x,y) sujeto a g(x,y)=0 */
lagrangian: f(x,y) - lambda*g(x,y)$
eqs: [diff(lagrangian,x)=0, diff(lagrangian,y)=0, g(x,y)=0]$
algsys(eqs, [x, y, lambda])$
```

---

## Resumen de fórmulas clave

### Tema 4

$$P_n(x) = \sum_{i=0}^{n} y_i L_i(x), \quad L_i(x) = \prod_{j\neq i}\frac{x-x_j}{x_i-x_j}$$

$$|f(x) - P_n(x)| \leq \frac{M_{n+1}}{(n+1)!}\left|\prod_{i=0}^n(x-x_i)\right|$$

### Tema 5

| Fórmula | Error |
|---------|-------|
| Punto medio: $h \cdot f(m)$ | $O(h^3)$ simple, $O(h^2)$ compuesta |
| Trapecio: $\frac{h}{2}[f(a)+f(b)]$ | $O(h^3)$ simple, $O(h^2)$ compuesta |
| Simpson: $\frac{h}{6}[f(a)+4f(m)+f(b)]$ | $O(h^5)$ simple, $O(h^4)$ compuesta |
| Gauss $n$ nodos | exacto para grado $\leq 2n-1$ |

**Errores exactos**:
$$E_T = -\frac{(b-a)^3}{12}f''(\xi), \quad E_S = -\frac{(b-a)^5}{2880}f^{(4)}(\xi)$$

$$E_{T,\text{comp}} = -\frac{(b-a)h^2}{12}f''(\xi), \quad E_{S,\text{comp}} = -\frac{(b-a)h^4}{180}f^{(4)}(\xi)$$

### Tema 6

$$\nabla f(x^*) = 0, \quad H_f(x^*) \succ 0 \Rightarrow \text{mínimo local}$$

$$\Delta_{\text{áurea}} = \frac{b-a}{\Phi}, \quad \Phi = \frac{\sqrt{5}+1}{2} \approx 1.618$$

$$d^{(k)} = -\nabla f(x^k) + \beta_k d^{(k-1)}, \quad \beta_k = \frac{\|\nabla f(x^k)\|^2}{\|\nabla f(x^{k-1})\|^2}$$

---

## Esquema de resolución por tipo de pregunta

### Interpolación (Tema 4)

```
1. ¿Cuántos nodos? → grado del polinomio = n-1
2. Calcular L_i(x) para Lagrange, o tabla diferencias para Newton
3. Si piden error: aplicar fórmula con M_{n+1}
4. Si piden nodos óptimos: Chebyshev
5. Si hay oscilaciones: splines o cambiar a Chebyshev
6. Si hay más puntos que parámetros: mínimos cuadrados
```

### Integración (Tema 5)

```
1. ¿Qué regla aplicar?
   - Pocos puntos dados → Newton-Cotes simple
   - Precisión O(h²) → Trapecio compuesta
   - Precisión O(h⁴) → Simpson compuesta
   - Máxima precisión → Gauss-Legendre
2. ¿Cuántos subintervalos para error < ε?
   - Despejar n de la fórmula de error
   - Calcular M₂ o M₄ = max|f''| o max|f⁽⁴⁾| en [a,b]
3. ¿Cambio de variable para Gauss? → x = ((b-a)t + (a+b))/2
```

### Optimización (Tema 6)

```
1. ¿Función de 1 variable sin restricciones?
   → f'=0, verificar f''>0 en el punto crítico
   → Si piden método numérico: sección áurea

2. ¿Función de n variables sin restricciones?
   → ∇f=0, verificar H definida positiva
   → Métodos iterativos: máxima pendiente o gradiente conjugado

3. ¿Con restricciones de igualdad?
   → Multiplicadores de Lagrange

4. ¿Programación lineal?
   → Forma estándar (añadir holguras/excesos/artificiales)
   → Símplex tabular: variable entrante (más negativo en c), saliente (ratio mínimo)
   → Terminar cuando c ≥ 0
```

---

## Conexiones con Parcial 1 (para el examen conjunto)

Si vas al Parcial 2 con los dos bloques, recuerda:

| Tema 1–3 | Tema 4–6 | Conexión |
|----------|----------|----------|
| Error aritmética finita | Error de interpolación | Ambos acotan errores con derivadas superiores |
| Newton-Raphson (raíces) | Gradiente conjugado (optimización) | Ambos son iterativos, cuadráticos/superlineales |
| Sistemas lineales | Gauss-Legendre, splines, símplex | Resolver sistemas $Ax=b$ aparece en todos |
| Punto fijo $g'(x)<1$ | $\rho(B)<1$ en iterativos | Mismo criterio de convergencia |
| Número de condición | Símplex (sensibilidad) | Sistemas mal condicionados afectan ambos |

---

*Generado: 2026-04-24 · Basado en: Tema5-Integracion.pdf (32 slides), Tema6-Optimizacion.pdf (75 slides), Prb_IntegraNumerica.pdf (8 problemas), apuntes sesiones PL*
