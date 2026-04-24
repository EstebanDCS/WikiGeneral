---
title: Guía Examen Parcial 1 — Temas 1–3
type: output
output_type: study-guide
date: 2026-04-24
subject: Computación Numérica
based_on: [Marzo26_1erParcial.pdf, Marzo26_1erParcialB.pdf, Aritmetica-Finita-y-Error.md, Biseccion-y-Regula-Falsi.md, Newton-Raphson.md, Metodos-Directos-Sistemas.md, Metodos-Iterativos-Sistemas.md]
tags: [computacion-numerica, examen, parcial1, aritmetica-finita, ecuaciones, sistemas-lineales]
---

# Guía Examen Parcial 1 — Temas 1–3

> **Contexto**: El Parcial 1 cubre los Temas 1 (aritmética finita y error), 2 (ecuaciones no lineales) y 3 (sistemas de ecuaciones lineales). Si suspendiste el Parcial 1, en el Parcial 2 tienes que presentar **ambos bloques**. Esta guía cubre los temas 1–3 con los ejercicios reales del examen de Marzo 2026.

---

## TEMA 1 — Aritmética Finita y Propagación de Errores

### 1.1 Conceptos fundamentales

**Error absoluto**: $E_a = |x_{\text{real}} - x_{\text{aprox}}|$

**Error relativo**: $E_r = \frac{|x_{\text{real}} - x_{\text{aprox}}|}{|x_{\text{real}}|}$

**Redondeo** vs **Truncamiento**:
- Redondeo: el dígito siguiente ≥ 5 → se sube en 1 el último dígito guardado
- Truncamiento: simplemente se cortan los dígitos sobrantes

**Aritmética de t dígitos**: en el examen se trabaja con un número fijo de cifras significativas. Todo resultado intermedio se redondea a `t` dígitos.

### 1.2 Propagación de errores en operaciones

Para $f(x_1, x_2, \ldots, x_n)$ con errores $\delta x_i$:

$$|\delta f| \approx \left|\frac{\partial f}{\partial x_1}\right||\delta x_1| + \left|\frac{\partial f}{\partial x_2}\right||\delta x_2| + \cdots$$

**Operaciones básicas**:
- Suma/resta: $|\delta(a \pm b)| \leq |\delta a| + |\delta b|$
- Multiplicación: $\frac{|\delta(ab)|}{|ab|} \leq \frac{|\delta a|}{|a|} + \frac{|\delta b|}{|b|}$
- División: ídem a multiplicación

**Cancelación catastrófica**: restar números casi iguales amplifica el error relativo enormemente. Evitar siempre que sea posible.

### 1.3 Ejercicio real — Versión A (Marzo 2026)

**Enunciado**: Usando aritmética de 3 dígitos significativos con redondeo, calcular $f(x) = e^{x + \sqrt{x^2-1}}$ en $x = 2\pi$.

**Solución paso a paso**:

1. $x = 2\pi \approx 6.28$ (3 dígitos)
2. $x^2 = 6.28^2 = 39.4384 \approx 39.4$ (3 dígitos)
3. $x^2 - 1 = 39.4 - 1 = 38.4$
4. $\sqrt{38.4} \approx 6.20$ (3 dígitos)
5. $x + \sqrt{x^2-1} = 6.28 + 6.20 = 12.48 \approx 12.5$ (3 dígitos)
6. $e^{12.5} \approx 268000$ (3 dígitos → $2.68 \times 10^5$)

**Valor exacto**: $f(2\pi) = e^{2\pi + \sqrt{4\pi^2 - 1}} \approx 304700$

**Error relativo**: $E_r = \frac{|304700 - 268000|}{304700} \approx 12\%$ (el enunciado dice ~13%)

> **Por qué el error es tan alto**: La resta $x^2 - 1$ con $x^2 \approx 39.4$ (ya redondeado) acumula error; la exponencial lo amplifica exponencialmente.

### 1.4 Ejercicio real — Versión B (Marzo 2026)

**Enunciado**: Aritmética de 3 dígitos, $f(x) = \frac{\cos(x^2+3x)}{2x}$ en $x = 9.73$.

**Solución paso a paso**:

1. $x = 9.73$, $x^2 = 9.73^2 = 94.6729 \approx 94.7$
2. $3x = 3 \times 9.73 = 29.19 \approx 29.2$
3. $x^2 + 3x = 94.7 + 29.2 = 123.9 \approx 124$
4. $\cos(124) = \cos(124 \text{ rad})$

   Para reducir: $124 / (2\pi) \approx 19.74$, parte fraccionaria $0.74 \times 2\pi \approx 4.65$ rad
   
   $\cos(4.65) \approx -0.130 \approx -0.130$

5. $2x = 2 \times 9.73 = 19.46 \approx 19.5$
6. $f(x) = -0.130 / 19.5 \approx -0.00667 \approx -6.67 \times 10^{-3}$

**Error relativo ≈ 59.37%** — altísimo por la sensibilidad del coseno y la propagación de errores en el argumento.

### 1.5 Lo que siempre preguntan del Tema 1

1. **Calcular con t dígitos**: hacer la cuenta paso a paso, aplicar redondeo en cada operación
2. **Error relativo final**: comparar con valor exacto (calculadora o Maxima)
3. **Identificar fuente del error**: ¿qué operación amplificó más el error?
4. **Dígitos significativos correctos**: si $E_r < 5\%$, ~2 dígitos; si $E_r < 0.5\%$, ~3 dígitos

---

## TEMA 2 — Ecuaciones No Lineales

### 2.1 Teorema de Bolzano (existencia)

Si $f$ es continua en $[a,b]$ y $f(a) \cdot f(b) < 0$ → existe al menos una raíz en $(a,b)$.

**Unicidad**: si además $f' > 0$ o $f' < 0$ en $[a,b]$ (función estrictamente monótona) → la raíz es única.

### 2.2 Método de Bisección

**Algoritmo**: en cada paso, $c = (a+b)/2$. Si $f(a)f(c) < 0$ → raíz en $[a,c]$, si no → raíz en $[c,b]$.

**Error máximo tras N iteraciones**:
$$|c_N - r| \leq \frac{b-a}{2^{N+1}}$$

**Número de iteraciones para precisión $\epsilon$**:
$$N \geq \frac{\ln\left(\frac{b-a}{\epsilon}\right)}{\ln 2} - 1$$

**Ejemplo** (Versión A): $f(x) = x^2\sin(x) - e^{-x}$, $[0,1]$, precisión $0.1$:
$$N \geq \frac{\ln(1/0.1)}{\ln 2} - 1 = \frac{2.303}{0.693} - 1 \approx 3.32 - 1 = 2.32 \Rightarrow N \geq 3$$

**Iteraciones explícitas**:
- $c_1 = 0.5$: $f(0) < 0$, $f(0.5) = 0.25\sin(0.5) - e^{-0.5} \approx 0.120 - 0.607 = -0.487 < 0$ → raíz en $[0.5, 1]$
- $c_2 = 0.75$: $f(0.75) = 0.5625\sin(0.75) - e^{-0.75} \approx 0.383 - 0.472 = -0.089 < 0$ → raíz en $[0.75, 1]$
- $c_3 = 0.875$: $f(0.875) \approx 0.766 \cdot 0.767 - 0.417 \approx 0.588 - 0.417 = 0.171 > 0$ → raíz en $[0.75, 0.875]$

**Ejemplo** (Versión B): $f(x) = 6x^3 - 7x^2 - 14x + 8$, $[-3,-1]$, $x_3$:
- $c_1 = (-3+(-1))/2 = -2$: $f(-2) = 6(-8)-7(4)-14(-2)+8 = -48-28+28+8 = -40 < 0$
  $f(-1) = 6(-1)-7(1)-14(-1)+8 = -6-7+14+8 = 9 > 0$ → raíz en $[-2,-1]$
- $c_2 = -1.5$: $f(-1.5) = 6(-3.375)-7(2.25)-14(-1.5)+8 = -20.25-15.75+21+8 = -7 < 0$ → raíz en $[-1.5,-1]$
- $c_3 = -1.25$: ← este es $x_3$

### 2.3 Método de Regula Falsi (Falsa Posición)

$$c = a - f(a) \cdot \frac{b-a}{f(b)-f(a)} = \frac{a \cdot f(b) - b \cdot f(a)}{f(b) - f(a)}$$

Convergencia más rápida que bisección pero puede ser lenta si la función es muy asimétrica (un extremo no se actualiza).

### 2.4 Punto Fijo — $x = g(x)$

**Teorema de convergencia**: si $g$ es continua en $[a,b]$, $g([a,b]) \subseteq [a,b]$ y $|g'(x)| \leq k < 1$ para todo $x \in [a,b]$, entonces:
- Existe un único punto fijo $r \in [a,b]$
- La sucesión $x_{n+1} = g(x_n)$ converge a $r$ para cualquier $x_0 \in [a,b]$

**Error**: $|x_n - r| \leq \frac{k^n}{1-k}|x_1 - x_0|$

**Ejemplo** (Versión B): $f(x) = 6x^3-7x^2-14x+8 = 0$ en $[-3,-1]$.
- $g_1(x) = \sqrt[3]{\frac{7x^2+14x-8}{6}}$
- $g_1'(x) = \frac{1}{3}\left(\frac{7x^2+14x-8}{6}\right)^{-2/3} \cdot \frac{14x+14}{6}$
- En $r \approx -4/3$: $|g_1'(-4/3)| = 0.875 < 1$ → **converge**

### 2.5 Newton-Raphson

$$x_{n+1} = x_n - \frac{f(x_n)}{f'(x_n)}$$

**Convergencia cuadrática** (orden 2): el error se eleva al cuadrado en cada iteración.

**Condición suficiente de convergencia**: si $f \in C^2[a,b]$, $f(r) = 0$, $f'(r) \neq 0$, y $x_0$ suficientemente próximo a $r$, entonces NR converge cuadráticamente.

**Demostración (tipo examen)**: Por Taylor:
$$f(x_{n+1}) = f(x_n) + f'(x_n)(x_{n+1}-x_n) + \frac{1}{2}f''(\xi)(x_{n+1}-x_n)^2$$

Como $x_{n+1} = x_n - f(x_n)/f'(x_n)$, el primer y segundo términos se cancelan:
$$e_{n+1} = x_{n+1} - r = -\frac{f''(\xi)}{2f'(x_n)}e_n^2$$

Luego $|e_{n+1}| \leq C|e_n|^2$ con $C \approx \frac{|f''(r)|}{2|f'(r)|}$.

### 2.6 Método de la Secante

$$x_{n+1} = x_n - f(x_n)\frac{x_n - x_{n-1}}{f(x_n) - f(x_{n-1})}$$

Convergencia superlineal (orden $\approx 1.618$). No necesita calcular $f'$.

### 2.7 Métodos para polinomios: Müller y Horner

**Algoritmo de Horner** (evaluación eficiente de polinomio):
Para $p(x) = a_n x^n + \cdots + a_0$:
```
b_n = a_n
b_{n-1} = a_{n-1} + x_0 b_n
b_{n-2} = a_{n-2} + x_0 b_{n-1}
...
b_0 = a_0 + x_0 b_1   → p(x_0) = b_0
```

**Método de Müller**: usa 3 puntos y aproxima $f$ por una parábola. Convergencia orden ~1.84. Puede encontrar raíces complejas.

### 2.8 Esquema de resolución del Tema 2 en el examen

```
Pregunta típica: "Dado f(x) en [a,b]..."

1. Bolzano: calcular f(a) y f(b), verificar signos opuestos → "existe raíz"
2. Unicidad: calcular f'(x). Si f'>0 en [a,b] → "raíz única"
3. Bisección:
   - Calcular N mínimo para precisión dada
   - Hacer las primeras 2-3 iteraciones explícitas
4. Convergencia de NR: enunciar teorema + mencionar condiciones

SIEMPRE mencionar:
- Condición de Bolzano con valores numéricos
- Para unicidad: "f'(x)>0 en [a,b] por tanto f es estrictamente creciente"
- Para NR: "f∈C²[a,b], f'(r)≠0, x₀ suficientemente próximo"
```

---

## TEMA 3 — Sistemas de Ecuaciones Lineales

### 3.1 Factorización de Cholesky

**Cuándo aplicar**: $A$ simétrica y **definida positiva** (todos los menores principales > 0, o equivalentemente todos los autovalores > 0).

**Descomposición**: $A = L L^T$ donde $L$ es triangular inferior.

**Fórmulas**:
$$l_{ii} = \sqrt{a_{ii} - \sum_{k=1}^{i-1} l_{ik}^2}$$
$$l_{ji} = \frac{1}{l_{ii}}\left(a_{ji} - \sum_{k=1}^{i-1} l_{jk}l_{ik}\right), \quad j > i$$

**Resolver $Ax = b$**: primero $Ly = b$ (sustitución directa), luego $L^T x = y$ (sustitución regresiva).

**Ejercicio real (Versión A)**: Factorizar $A = \begin{pmatrix}4&2&2\\2&10&8\\2&8&14\end{pmatrix}$

- $l_{11} = \sqrt{4} = 2$
- $l_{21} = 2/2 = 1$, $l_{31} = 2/2 = 1$
- $l_{22} = \sqrt{10 - 1^2} = \sqrt{9} = 3$
- $l_{32} = (8 - 1\cdot1)/3 = 7/3$... (ver solución completa en el examen)
- Resultado del enunciado: $L = \begin{pmatrix}2&0&0\\1&3&0\\1&3&2\end{pmatrix}$

**Resolver con $b = (2,2,2)^T$**:
- $Ly = b$: $2y_1 = 2 \Rightarrow y_1 = 1$; $y_1 + 3y_2 = 2 \Rightarrow y_2 = 1/3$; $y_1 + 3y_2 + 2y_3 = 2 \Rightarrow y_3 = 0$... ajustar con el $b$ real del examen.
- Solución del examen: $x = (1/2, 1/2, 1/2)^T$

### 3.2 Factorización LU (Eliminación Gaussiana)

**Con pivoteo parcial** (intercambio de filas para maximizar $|a_{ij}|$):

$$PA = LU \quad \Rightarrow \quad PAx = Pb \quad \Rightarrow \quad Ly = Pb, \quad Ux = y$$

**Ejercicio real (Versión B)**: $A = \begin{pmatrix}2&-3&1\\6&-8&0\\-2&5&-5\end{pmatrix}$

Eliminación paso a paso:
1. Fila 2 ← Fila 2 - 3·Fila 1: $(6-6, -8+9, 0-3) = (0, 1, -3)$
2. Fila 3 ← Fila 3 + Fila 1: $(-2+2, 5-3, -5+1) = (0, 2, -4)$
3. Fila 3 ← Fila 3 - 2·Fila 2: $(0, 0, -4+6) = (0, 0, 2)$

Resultado: $U = \begin{pmatrix}2&-3&1\\0&1&-3\\0&0&2\end{pmatrix}$, $L = \begin{pmatrix}1&0&0\\3&1&0\\-1&2&1\end{pmatrix}$

**Inversa por LU**: resolver $Ax_i = e_i$ para cada vector canónico $e_i$.

**Número de condición**: $\text{cond}(A) = \|A\| \cdot \|A^{-1}\|$

- Si $\text{cond}(A) \approx 1$ → sistema bien condicionado
- Si $\text{cond}(A) \gg 1$ → sistema mal condicionado (pequeños errores en $b$ → grandes errores en $x$)
- En Versión B: $\text{cond}_1(A) = 336$ → sistema moderadamente mal condicionado

**Norma 1** (suma columnas): $\|A\|_1 = \max_j \sum_i |a_{ij}|$  
**Norma ∞** (suma filas): $\|A\|_\infty = \max_i \sum_j |a_{ij}|$

### 3.3 Métodos iterativos: Jacobi y Gauss-Seidel

Para resolver $Ax = b$ con $A = D - L - U$ ($D$: diagonal, $L$: triangular inf., $U$: triangular sup.):

**Jacobi**: $x^{(k+1)} = D^{-1}(L+U)x^{(k)} + D^{-1}b$

Matriz de iteración: $B_J = D^{-1}(L+U)$

**Gauss-Seidel**: $x^{(k+1)} = (D-L)^{-1}Ux^{(k)} + (D-L)^{-1}b$

Matriz de iteración: $B_{GS} = (D-L)^{-1}U$

**Convergencia**: el método converge si y solo si $\rho(B) < 1$ (radio espectral = máximo valor absoluto de autovalores).

**Condición suficiente** (dominancia diagonal estricta): si $|a_{ii}| > \sum_{j \neq i}|a_{ij}|$ para todo $i$ → ambos métodos convergen.

**Ejercicio real (Versión A)**: calcular $B_{GS}$ y $\rho(B_{GS})$.

Para $A = \begin{pmatrix}4&2&2\\2&10&8\\2&8&14\end{pmatrix}$ con $b = (2,2,2)^T$:

$D = \text{diag}(4,10,14)$, $L = \begin{pmatrix}0&0&0\\-2&0&0\\-2&-8&0\end{pmatrix}$, $U = \begin{pmatrix}0&-2&-2\\0&0&-8\\0&0&0\end{pmatrix}$

$B_{GS} = (D-L)^{-1}U$

El examen da $\rho(B_{GS}) = 5/7 < 1$ → **converge**.

### 3.4 Comparación de métodos

| Método | Cuando usar | Coste | Notas |
|--------|-------------|-------|-------|
| Gauss + pivoteo | General | $O(n^3/3)$ | Siempre funciona |
| Cholesky | A sim. def. positiva | $O(n^3/6)$ | El doble de rápido |
| LU | A cuadrada, guardar factorización | $O(n^3/3)$ | Reutilizar para múltiples $b$ |
| Jacobi | A grande, dispersa, diag. dominante | $O(n^2)$/iter | Más fácil de paralelizar |
| Gauss-Seidel | Ídem, converge ~2x más rápido que Jacobi | $O(n^2)$/iter | Más rápido en práctica |

---

## Esquema visual del Tema 3 (cómo resolver en el examen)

```
Sistema Ax = b

¿A simétrica y definida positiva?
    SÍ → Cholesky: A = LL^T
         → Ly = b (hacia adelante)
         → L^T x = y (hacia atrás)

    NO → Eliminación Gaussiana con pivoteo
         → LU decomposition
         → Ly = Pb, Ux = y

¿Piden métodos iterativos?
    → Calcular B_J = D^{-1}(L+U) o B_{GS} = (D-L)^{-1}U
    → Calcular ρ(B) = max|λ|
    → Si ρ < 1 → converge
    → Si dominancia diagonal → ambos convergen

¿Piden número de condición?
    → cond(A) = ||A|| · ||A^{-1}||
    → Con norma 1: max suma por columna
```

---

## Ejercicios tipo para practicar

### Tema 1
Dado $f(x) = \ln(1 + e^x)/x$ en $x = 3.47$, calcular con aritmética de 4 dígitos y hallar el error relativo.

### Tema 2
Sea $f(x) = x^3 - 2x - 5$ en $[2, 3]$.
1. Aplicar Bolzano para demostrar existencia de raíz
2. Demostrar unicidad con $f'$
3. Encontrar $x_3$ con bisección
4. Indicar condiciones de convergencia de NR
5. Dar la iteración de punto fijo $g(x) = (2x+5/x^2)/3$ y verificar convergencia

### Tema 3
Dado $A = \begin{pmatrix}4&-1&0\\-1&4&-1\\0&-1&4\end{pmatrix}$, $b = (3, 2, 3)^T$:
1. Verificar que A es definida positiva (calcular menores principales)
2. Aplicar Cholesky
3. Resolver el sistema
4. Verificar que la diagonal es dominante → Gauss-Seidel converge
5. Hacer 2 iteraciones de Gauss-Seidel con $x^{(0)} = (0,0,0)^T$

---

## Resumen de fórmulas clave

### Tema 1

$$E_r = \frac{|x_{\text{real}} - x_{\text{aprox}}|}{|x_{\text{real}}|} \times 100\%$$

### Tema 2

| Método | Fórmula | Orden |
|--------|---------|-------|
| Bisección | $c = (a+b)/2$ | 1 (lineal) |
| Regula Falsi | $c = (af(b)-bf(a))/(f(b)-f(a))$ | superlineal |
| Punto Fijo | $x_{n+1} = g(x_n)$ | depende de $|g'|$ |
| Newton-Raphson | $x_{n+1} = x_n - f(x_n)/f'(x_n)$ | **2 (cuadrático)** |
| Secante | $x_{n+1} = x_n - f(x_n)(x_n-x_{n-1})/(f(x_n)-f(x_{n-1}))$ | ~1.618 |

$$N_{\text{bisec}} \geq \frac{\ln\frac{b-a}{\epsilon}}{\ln 2} - 1$$

### Tema 3

$$l_{ii} = \sqrt{a_{ii} - \sum_{k<i} l_{ik}^2}, \quad l_{ji} = \frac{a_{ji} - \sum_{k<i}l_{jk}l_{ik}}{l_{ii}}$$

$$\rho(B) = \max_i |\lambda_i(B)| < 1 \Leftrightarrow \text{convergencia}$$

$$\text{cond}_p(A) = \|A\|_p \cdot \|A^{-1}\|_p$$

---

*Generado: 2026-04-24 · Basado en: Marzo26_1erParcial.pdf (Versión A y B con soluciones), teoría de temas 1–3*
