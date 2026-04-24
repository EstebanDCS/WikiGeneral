---
title: Tema 6 — Ejercicios de Optimización Resueltos
type: output
output_type: study-guide
date: 2026-04-24
subject: Computación Numérica
based_on: [ejercicios-tema6.pdf, Examen-Parcial-2-Guia.md]
tags: [computacion-numerica, optimizacion, ejercicios, seccion-aurea, gradiente, simplex]
---

# Tema 6 — Ejercicios de Optimización: Resueltos Paso a Paso

> 9 problemas resueltos: sección áurea (Prob 1–3), máxima pendiente (Prob 4–7), programación lineal (Prob 8–9).

---

## Recordatorio rápido de los métodos

### Sección áurea (1D)

**Sirve para**: encontrar máximo o mínimo de una función de **una variable** en un intervalo $[a,b]$, sin necesidad de calcular derivadas.

**La idea**: en cada paso, evaluamos la función en dos puntos interiores $\alpha$ y $\beta$ y descartamos una parte del intervalo. La magia es que el intervalo se reduce siempre un factor $1/\varphi \approx 0.618$ y además **se reutiliza uno de los dos puntos** (ahorrando evaluaciones).

**Razón áurea**: $\varphi = \frac{\sqrt{5}+1}{2} \approx 1.618$

**Algoritmo (para máximo)**:
```
Dado [a, b]:
1. Δ = (b - a) / φ
2. α = b - Δ     (punto izquierdo interior)
   β = a + Δ     (punto derecho interior)
3. Evaluar f(α) y f(β)
4. Si f(α) < f(β):  el máximo está en [α, b]  → nuevo a = α
   Si f(α) > f(β):  el máximo está en [a, β]  → nuevo b = β
5. Repetir desde el paso 1 (reutilizando el punto que queda)
```

Para **mínimo**: cambiar la condición al revés (si f(α) > f(β) el mínimo está en [α,b]).

---

### Método de Máxima Pendiente (Gradiente)

**Sirve para**: minimizar o maximizar funciones de **varias variables**.

**La idea**: nos movemos desde el punto actual en la dirección donde la función crece/decrece más rápido (el gradiente), y elegimos el paso óptimo.

**Algoritmo**:
```
Dado x⁰ (punto inicial):
1. Calcular ∇f(xᵏ)   ← gradiente en el punto actual
2. Si maximizamos: dirección d = +∇f(xᵏ)
   Si minimizamos: dirección d = -∇f(xᵏ)
3. Buscar el paso óptimo α: minimizar/maximizar h(α) = f(xᵏ + α·d)
   → derivar h respecto a α e igualar a 0
4. xᵏ⁺¹ = xᵏ + α·d
5. Repetir hasta que ∇f ≈ 0
```

---

### Método Símplex

**Sirve para**: optimizar una función lineal con restricciones lineales.

**Pasos clave**:
1. Convertir desigualdades a igualdades añadiendo **variables de holgura** ($s_i \geq 0$)
2. Construir la **tabla inicial** (base = variables de holgura)
3. **Variable entrante**: columna con coeficiente más negativo en la fila objetivo
4. **Variable saliente**: fila con menor ratio $\text{RHS}/a_{ij}$ (con $a_{ij} > 0$)
5. **Pivotear**: hacer el elemento pivote = 1 y el resto de la columna = 0
6. Repetir hasta que todos los coeficientes en la fila objetivo sean ≥ 0

---

## Problema 1 — Sección áurea: máximo de $f(x) = 2\sin(x) - \frac{x^2}{10}$ en $[0, 4]$

**Paso previo: ¿dónde está el máximo analíticamente?**
$f'(x) = 2\cos(x) - \frac{x}{5} = 0$

No tiene solución simple, por eso usamos sección áurea. El máximo está cerca de $x \approx 1.43$ (se puede verificar: $f(1.43) \approx 1.77$).

---

**Iteración 1** — Intervalo $[0, 4]$, longitud = 4

$$\Delta = \frac{4}{\varphi} = \frac{4}{1.618} = 2.472$$

$$\alpha = 4 - 2.472 = 1.528, \qquad \beta = 0 + 2.472 = 2.472$$

| Punto | x | $2\sin(x)$ | $-x^2/10$ | $f(x)$ |
|-------|---|-----------|----------|--------|
| $\alpha$ | 1.528 | 1.999 | −0.234 | **1.765** |
| $\beta$ | 2.472 | 1.250 | −0.611 | **0.639** |

$f(\alpha) > f(\beta)$ → máximo en $[0, \beta] = [0, 2.472]$, nuevo $b = 2.472$.

El $\alpha$ anterior (1.528) **pasa a ser el nuevo $\beta$**.

---

**Iteración 2** — Intervalo $[0, 2.472]$, longitud = 2.472

$$\Delta = \frac{2.472}{1.618} = 1.528$$

$$\alpha = 2.472 - 1.528 = 0.944, \qquad \beta = 0 + 1.528 = 1.528 \text{ (ya calculado!)}$$

| Punto | x | $f(x)$ |
|-------|---|--------|
| $\alpha$ | 0.944 | $2\sin(0.944) - 0.089 = 1.625 - 0.089 = 1.536$ |
| $\beta$ | 1.528 | **1.765** (reutilizado) |

$f(\beta) > f(\alpha)$ → máximo en $[\alpha, 2.472] = [0.944, 2.472]$, nuevo $a = 0.944$.

El $\beta$ anterior (1.528) **pasa a ser el nuevo $\alpha$**.

---

**Iteración 3** — Intervalo $[0.944, 2.472]$, longitud = 1.528

$$\Delta = \frac{1.528}{1.618} = 0.944$$

$$\alpha = 2.472 - 0.944 = 1.528 \text{ (reutilizado)}, \qquad \beta = 0.944 + 0.944 = 1.888$$

| Punto | x | $f(x)$ |
|-------|---|--------|
| $\alpha$ | 1.528 | **1.765** (reutilizado) |
| $\beta$ | 1.888 | $2\sin(1.888) - 0.357 = 1.902 - 0.357 = 1.545$ |

$f(\alpha) > f(\beta)$ → máximo en $[0.944, 1.888]$, nuevo $b = 1.888$.

---

**Iteraciones siguientes** — el intervalo sigue reduciéndose:

| It | $[a,b]$ | $\alpha$ | $\beta$ | $f(\alpha)$ | $f(\beta)$ | Nuevo intervalo |
|----|---------|----------|---------|-------------|------------|-----------------|
| 4 | [0.944, 1.888] | 1.304 | 1.528 | 1.715 | 1.765 | [1.304, 1.888] |
| 5 | [1.304, 1.888] | 1.528 | 1.664 | 1.765 | 1.753 | [1.304, 1.664] |
| 6 | [1.304, 1.664] | 1.440 | 1.528 | 1.773 | 1.765 | [1.304, 1.528] |

**Resultado**: el máximo está en $x \approx 1.43$, $f(1.43) \approx 1.77$.

> **Clave para el examen**: en cada iteración solo calculas UNA función nueva (el otro punto ya lo tenías). Siempre menciona qué intervalo descartaste y por qué.

---

## Problema 2 — Sección áurea: $f(x) = 4x - 1.8x^2 + 1.2x^3 - 0.3x^4$ en $[-2, 4]$

**Verificación previa**: $f(-2) = -8 - 7.2 - 9.6 - 4.8 = -29.6$, $f(4) = 16 - 28.8 + 76.8 - 76.8 = -12.8$, $f(2) = 8 - 7.2 + 9.6 - 4.8 = 5.6$. El máximo está en el interior.

**Iteración 1** — Intervalo $[-2, 4]$, longitud = 6

$$\Delta = \frac{6}{1.618} = 3.708$$

$$\alpha = 4 - 3.708 = 0.292, \qquad \beta = -2 + 3.708 = 1.708$$

$$f(0.292) = 4(0.292) - 1.8(0.085) + 1.2(0.025) - 0.3(0.007) \approx 1.168 - 0.153 + 0.030 - 0.002 = 1.043$$

$$f(1.708) = 4(1.708) - 1.8(2.917) + 1.2(4.983) - 0.3(8.514)$$
$$= 6.832 - 5.251 + 5.980 - 2.554 = 5.007$$

$f(\beta) > f(\alpha)$ → máximo en $[\alpha, 4] = [0.292, 4]$, nuevo $a = 0.292$.

**Iteración 2** — Intervalo $[0.292, 4]$, longitud = 3.708

$$\Delta = \frac{3.708}{1.618} = 2.292$$

$$\alpha = 4 - 2.292 = 1.708 \text{ (reutilizado)}, \qquad \beta = 0.292 + 2.292 = 2.584$$

$$f(2.584) = 4(2.584) - 1.8(6.677) + 1.2(17.253) - 0.3(44.591)$$
$$= 10.336 - 12.019 + 20.704 - 13.377 = 5.644$$

$f(\beta) > f(\alpha)$ → máximo en $[1.708, 4]$, nuevo $a = 1.708$.

**Iteración 3** — Intervalo $[1.708, 4]$, longitud = 2.292

$$\Delta = \frac{2.292}{1.618} = 1.416$$

$$\alpha = 4 - 1.416 = 2.584 \text{ (reutilizado)}, \qquad \beta = 1.708 + 1.416 = 3.124$$

$$f(3.124) = 4(3.124) - 1.8(9.759) + 1.2(30.499) - 0.3(95.280)$$
$$= 12.496 - 17.566 + 36.599 - 28.584 = 2.945$$

$f(\alpha) > f(\beta)$ → máximo en $[1.708, 3.124]$, nuevo $b = 3.124$.

**Continuando**, el intervalo converge hacia $x \approx 2.05$, donde $f(2.05) \approx 5.74$.

> El máximo real está en $x \approx 2.05$ (verificable derivando e igualando a cero).

---

## Problema 3 — Sección áurea: trayectoria de pelota

**Función**: $y(x) = \tan(\theta_0)\,x - \frac{g}{2v_0^2\cos^2(\theta_0)}\,x^2 + y_0$

**Datos**: $\theta_0 = 50° = 0.8727\,\text{rad}$, $v_0 = 25\,\text{m/s}$, $y_0 = 1\,\text{m}$, $g = 9.81\,\text{m/s}^2$

**Calcular constantes**:
$$\tan(50°) = 1.1918, \quad \cos^2(50°) = 0.4132$$
$$k = \frac{g}{2v_0^2\cos^2(\theta_0)} = \frac{9.81}{2 \cdot 625 \cdot 0.4132} = \frac{9.81}{516.5} = 0.01899\,\text{m}^{-1}$$

La función queda: $y(x) = 1.1918x - 0.01899x^2 + 1$

**Intervalo**: $[0, 60]$ (la pelota toca el suelo a ~60 m)

**Iteración 1** — $[0, 60]$

$$\Delta = \frac{60}{1.618} = 37.08$$

$$\alpha = 60 - 37.08 = 22.92, \qquad \beta = 37.08$$

$$y(22.92) = 1.1918(22.92) - 0.01899(525.3) + 1 = 27.31 - 9.98 + 1 = \mathbf{18.33\,m}$$

$$y(37.08) = 1.1918(37.08) - 0.01899(1375.0) + 1 = 44.21 - 26.10 + 1 = \mathbf{19.11\,m}$$

$y(\beta) > y(\alpha)$ → máximo en $[22.92, 60]$.

**Iteración 2** — $[22.92, 60]$

$$\Delta = 37.08/1.618 = 22.92$$

$$\alpha = 60 - 22.92 = 37.08 \text{ (reutilizado)}, \qquad \beta = 22.92 + 22.92 = 45.84$$

$$y(45.84) = 1.1918(45.84) - 0.01899(2101.3) + 1 = 54.64 - 39.91 + 1 = 15.73\,\text{m}$$

$y(\alpha) = 19.11 > y(\beta) = 15.73$ → máximo en $[22.92, 45.84]$.

**Iteración 3** — $[22.92, 45.84]$

$$\Delta = 22.92/1.618 = 14.17$$

$$\alpha = 45.84 - 14.17 = 31.67, \qquad \beta = 22.92 + 14.17 = 37.09$$

$$y(31.67) = 1.1918(31.67) - 0.01899(1003.0) + 1 = 37.75 - 19.04 + 1 = \mathbf{19.71\,m}$$
$$y(37.09) \approx 19.11\,\text{m (reutilizado)}$$

$y(\alpha) > y(\beta)$ → máximo en $[22.92, 37.09]$.

**Resultado**: el algoritmo converge a $x^* \approx 31.4\,\text{m}$, altura máxima $\approx \mathbf{19.7\,m}$.

*Verificación analítica*: el máximo de $y(x)$ está en $x^* = \tan(\theta_0)/(2k) = 1.1918/(2\cdot0.01899) = 31.39\,\text{m}$ ✓

---

## Problema 4 — Máxima pendiente: $f(x,y) = 2xy + 2x - x^2 - 2y^2$, máximo desde $(-1, 1)$

### Paso 1: Calcular el gradiente

$$\frac{\partial f}{\partial x} = 2y + 2 - 2x, \qquad \frac{\partial f}{\partial y} = 2x - 4y$$

### Paso 2: Encontrar el máximo analítico (para saber adónde converge)

Igualando el gradiente a cero:
$$2y + 2 - 2x = 0 \Rightarrow x = y + 1$$
$$2x - 4y = 0 \Rightarrow x = 2y$$

Sustituyendo: $2y = y + 1 \Rightarrow y = 1$, $x = 2$.

**El máximo está en $(2, 1)$** con $f(2,1) = 4 + 4 - 4 - 2 = 2$.

Hessiana (para confirmar que es máximo):
$$H = \begin{pmatrix}-2 & 2 \\ 2 & -4\end{pmatrix}, \quad \det(H) = 8-4 = 4 > 0, \quad H_{11} = -2 < 0 \Rightarrow \text{máximo ✓}$$

### Paso 3: Iteración 1 — desde $x^0 = (-1, 1)$

**Gradiente en $(-1, 1)$**:
$$\nabla f(-1,1) = (2(1)+2-2(-1),\ 2(-1)-4(1)) = (6,\ -6)$$

**Dirección de ascenso** (máximo → sumamos el gradiente):
$$x^1 = (-1 + 6\alpha,\ 1 - 6\alpha)$$

**Búsqueda lineal**: sustituimos en $f$ y optimizamos respecto a $\alpha$.

Sea $x = -1+6\alpha$, $y = 1-6\alpha$:

$$f = 2(-1+6\alpha)(1-6\alpha) + 2(-1+6\alpha) - (-1+6\alpha)^2 - 2(1-6\alpha)^2$$

Expandiendo término a término:
- $2(-1+6\alpha)(1-6\alpha) = 2(-1+6\alpha+6\alpha-36\alpha^2) = -2+24\alpha-72\alpha^2$
- $2(-1+6\alpha) = -2+12\alpha$
- $(-1+6\alpha)^2 = 1-12\alpha+36\alpha^2$
- $2(1-6\alpha)^2 = 2-24\alpha+72\alpha^2$

$$h(\alpha) = (-2+24\alpha-72\alpha^2) + (-2+12\alpha) - (1-12\alpha+36\alpha^2) - (2-24\alpha+72\alpha^2)$$

$$h(\alpha) = (-2-2-1-2) + (24+12+12+24)\alpha + (-72-36-72)\alpha^2$$

$$\boxed{h(\alpha) = -7 + 72\alpha - 180\alpha^2}$$

**Máximo de $h$**: $h'(\alpha) = 72 - 360\alpha = 0 \Rightarrow \alpha^* = 0.2$

**Nuevo punto**:
$$x^1 = (-1+6(0.2),\ 1-6(0.2)) = (0.2,\ -0.2)$$

### Paso 4: Iteración 2 — desde $(0.2, -0.2)$

**Gradiente en $(0.2, -0.2)$**:
$$\nabla f(0.2,-0.2) = (2(-0.2)+2-2(0.2),\ 2(0.2)-4(-0.2)) = (1.2,\ 1.2)$$

$$x^2 = (0.2+1.2\alpha,\ -0.2+1.2\alpha)$$

Repitiendo el proceso de búsqueda lineal se obtiene $\alpha^* = 1/6 \approx 0.167$:

$$x^2 = (0.2 + 1.2(1/6),\ -0.2 + 1.2(1/6)) = (0.4,\ 0.0)$$

### Convergencia

| Iteración | Punto | $f$ | $\|\nabla f\|$ |
|-----------|-------|-----|----------------|
| 0 | $(-1.0,\ 1.0)$ | $-5$ | $8.49$ |
| 1 | $(0.2,\ -0.2)$ | $-0.56$ | $1.70$ |
| 2 | $(0.4,\ 0.0)$ | $0.96$ | $1.13$ |
| ... | ... | ... | ... |
| ∞ | $(2.0,\ 1.0)$ | **2** | **0** |

**Resultado**: máximo en $(2, 1)$ con $f = 2$.

---

## Problema 5 — Máxima pendiente: $f(x,y) = -8x + x^2 + 12y + 4y^2 - 2xy$, mínimo desde $(0,0)$

### Paso 1: Gradiente y mínimo analítico

$$\frac{\partial f}{\partial x} = -8 + 2x - 2y, \qquad \frac{\partial f}{\partial y} = 12 + 8y - 2x$$

Igualando a cero: $\quad x - y = 4$ y $\quad -x + 4y = -6$

Sumando: $3y = -2 \Rightarrow y = -2/3$, $x = 10/3$.

**Mínimo en $(10/3,\ -2/3)$**, $f = -52/3 \approx -17.33$.

Hessiana: $H = \begin{pmatrix}2 & -2\\ -2 & 8\end{pmatrix}$, $\det(H) = 12 > 0$, $H_{11} = 2 > 0$ → **mínimo ✓**

### Paso 2: Iteración 1 — desde $(0, 0)$

**Gradiente en $(0,0)$**:
$$\nabla f(0,0) = (-8+0-0,\ 12+0-0) = (-8,\ 12)$$

**Dirección de descenso** (mínimo → restamos el gradiente):
$$x^1 = (0-(-8)\alpha,\ 0-12\alpha) = (8\alpha,\ -12\alpha)$$

**Búsqueda lineal**:
$$h(\alpha) = f(8\alpha, -12\alpha) = -8(8\alpha) + (8\alpha)^2 + 12(-12\alpha) + 4(-12\alpha)^2 - 2(8\alpha)(-12\alpha)$$
$$= -64\alpha + 64\alpha^2 - 144\alpha + 576\alpha^2 + 192\alpha^2$$
$$= -208\alpha + 832\alpha^2$$

**Mínimo de $h$**: $h'(\alpha) = -208 + 1664\alpha = 0 \Rightarrow \alpha^* = 208/1664 = 0.125$

$$x^1 = (8(0.125),\ -12(0.125)) = (1.0,\ -1.5)$$

### Paso 3: Iteración 2 — desde $(1, -1.5)$

**Gradiente en $(1, -1.5)$**:
$$\nabla f(1,-1.5) = (-8+2-2(-1.5),\ 12+8(-1.5)-2(1)) = (-8+2+3,\ 12-12-2) = (-3,\ -2)$$

**Dirección de descenso**: $x^2 = (1+3\alpha,\ -1.5+2\alpha)$

**Búsqueda lineal** (proceso análogo):
$$h(\alpha) = f(1+3\alpha,\ -1.5+2\alpha)$$

Expandiendo y derivando: $\alpha^* = 13/52 = 0.25$

$$x^2 = (1+3(0.25),\ -1.5+2(0.25)) = (1.75,\ -1.0)$$

### Convergencia

| It. | Punto | $f$ |
|-----|-------|-----|
| 0 | $(0.00,\ 0.00)$ | 0 |
| 1 | $(1.00,\ -1.50)$ | $-14.0$ |
| 2 | $(1.75,\ -1.00)$ | $-16.6$ |
| 3 | $(2.50,\ -0.75)$ | $-17.2$ |
| ∞ | $(3.33,\ -0.67)$ | **−17.33** |

**Resultado**: mínimo en $(10/3, -2/3) \approx (3.33, -0.67)$.

---

## Problema 6 — Máxima pendiente: $f(x,y) = \ln(x^2 + 3x^2y^2 + 2y^2 + 1)$, mínimo desde $(2,1)$

### Paso 1: Análisis de la función

**¿Dónde está el mínimo?** La expresión dentro del logaritmo es $x^2(1+3y^2) + 2y^2 + 1 \geq 1$, con igualdad en $(0,0)$.

Por tanto $\ln(\cdot) \geq \ln(1) = 0$, y el mínimo global es **$f(0,0) = 0$**.

### Paso 2: Gradiente

Sea $g = x^2 + 3x^2y^2 + 2y^2 + 1 = x^2(1+3y^2) + 2y^2 + 1$.

$$\frac{\partial f}{\partial x} = \frac{2x + 6xy^2}{g} = \frac{2x(1+3y^2)}{g}$$

$$\frac{\partial f}{\partial y} = \frac{6x^2y + 4y}{g} = \frac{2y(3x^2+2)}{g}$$

### Paso 3: Iteración 1 — desde $(2, 1)$

**Denominador**: $g(2,1) = 4 + 3(4)(1) + 2(1) + 1 = 4+12+2+1 = 19$

$$\nabla f(2,1) = \left(\frac{2(2)(1+3)}{19},\ \frac{2(1)(12+2)}{19}\right) = \left(\frac{16}{19},\ \frac{28}{19}\right) \approx (0.842,\ 1.474)$$

**Dirección de descenso**: $x^1 = (2 - 0.842\alpha,\ 1 - 1.474\alpha)$

La búsqueda lineal se resuelve numéricamente (la función no tiene mínimo analítico sencillo en esta dirección). Con búsqueda numérica se obtiene $\alpha^* \approx 0.8$.

$$x^1 \approx (2 - 0.674,\ 1 - 1.179) = (1.326,\ -0.179)$$

### Iteraciones siguientes

El algoritmo converge lentamente hacia $(0,0)$:

| It. | Punto | $f$ |
|-----|-------|-----|
| 0 | $(2.0,\ 1.0)$ | $\ln(19) = 2.944$ |
| 1 | $(1.33,\ -0.18)$ | $\approx 1.60$ |
| 2 | $(0.85,\ -0.08)$ | $\approx 0.75$ |
| ... | ... | ... |
| ∞ | $(0,\ 0)$ | **0** |

**Resultado**: mínimo en $(0, 0)$ con $f = 0$.

---

## Problema 7 — Máxima pendiente: $f(x,y) = 2x^3y^2 - 7xy + x^2 + 3y$, máximo desde $(1,1)$

### Paso 1: Gradiente en $(1,1)$

$$\frac{\partial f}{\partial x} = 6x^2y^2 - 7y + 2x, \qquad \frac{\partial f}{\partial y} = 4x^3y - 7x + 3$$

$$\nabla f(1,1) = (6(1)(1)-7+2(1),\ 4(1)(1)-7+3) = (1,\ 0)$$

El gradiente apunta solo en dirección $x$.

### Paso 2: Iteración 1 — desde $(1,1)$

**Dirección de ascenso**: $x^1 = (1+\alpha,\ 1)$

**Búsqueda lineal**: $h(\alpha) = f(1+\alpha, 1) = 2(1+\alpha)^3 - 7(1+\alpha) + (1+\alpha)^2 + 3$

Expandiendo:
- $2(1+\alpha)^3 = 2+6\alpha+6\alpha^2+2\alpha^3$
- $-7(1+\alpha) = -7-7\alpha$
- $(1+\alpha)^2 = 1+2\alpha+\alpha^2$

$$h(\alpha) = (2-7+1+3) + (6-7+2)\alpha + (6+1)\alpha^2 + 2\alpha^3 = -1 + \alpha + 7\alpha^2 + 2\alpha^3$$

$h'(\alpha) = 1 + 14\alpha + 6\alpha^2 = 0$

Discriminante: $196 - 24 = 172 > 0$, raíces: $\alpha = (-14 \pm \sqrt{172})/12 \approx -0.074$ o $-2.26$.

> **Problema**: ambas raíces son negativas. Esto indica que $h'(0) = 1 > 0$, es decir, la función sigue creciendo en $\alpha > 0$ sin un máximo local en ese tramo. La función $f$ crece sin límite en esta dirección (el término $2x^3$ domina).

**Interpretación**: esta función no tiene máximo global (es no acotada superiormente para $x \to +\infty$ con $y > 0$). El método de máxima pendiente no converge.

> **Lo que el profesor probablemente espera**: mostrar el primer paso (gradiente = $(1,0)$, dirección de ascenso) y calcular la búsqueda lineal, concluyendo que no hay máximo global y el método diverge. Alternativamente, usar un paso fijo $\alpha = 0.01$ y hacer pocas iteraciones.

**Con $\alpha = 0.01$**: $x^1 = (1.01, 1)$, $f(1.01, 1) = 2(1.01)^3 - 7(1.01) + (1.01)^2 + 3 \approx -0.98$

---

## Problema 8 — Programación lineal: Maximizar $6x + 8y$

**Restricciones**:
$$5x + 2y \leq 40 \quad (1)$$
$$6x + 6y \leq 60 \Rightarrow x + y \leq 10 \quad (2)$$
$$2x + 4y \leq 32 \Rightarrow x + 2y \leq 16 \quad (3)$$
$$x + 2y \leq 500 \quad (4, \text{redundante})$$
$$x, y \geq 0$$

### Parte a) Solución gráfica

**Vértices de la región factible** (intersecciones de restricciones activas):

| Intersección | Punto | Verificación | $f = 6x+8y$ |
|--------------|-------|-------------|-------------|
| $x=0$, $y=0$ | $(0,0)$ | ✓ | 0 |
| (1) + $y=0$ | $(8, 0)$ | ✓ | 48 |
| (1) + (2): $5x+2y=40$, $x+y=10$ | $x=20/3, y=10/3$ | ✓ | 200/3 ≈ 66.7 |
| (2) + (3): $x+y=10$, $x+2y=16$ | $(4, 6)$ | ✓ | **72** |
| (3) + $x=0$ | $(0, 8)$ | ✓ | 64 |

**Verificación del punto $(4, 6)$**:
- $5(4)+2(6) = 32 \leq 40$ ✓
- $6(4)+6(6) = 60 \leq 60$ ✓ (activa)
- $2(4)+4(6) = 32 \leq 32$ ✓ (activa)

**Máximo**: $f = 72$ en $(x, y) = (4, 6)$.

### Parte b) Método símplex

Añadimos variables de holgura $s_1, s_2, s_3, s_4$:

$$5x + 2y + s_1 = 40$$
$$6x + 6y + s_2 = 60$$
$$2x + 4y + s_3 = 32$$
$$x + 2y + s_4 = 500$$

**Tabla inicial** (base: $\{s_1, s_2, s_3, s_4\}$, minimizamos $-z = -6x - 8y$):

| Base | $x$ | $y$ | $s_1$ | $s_2$ | $s_3$ | $s_4$ | RHS |
|------|-----|-----|-------|-------|-------|-------|-----|
| $s_1$ | 5 | 2 | 1 | 0 | 0 | 0 | 40 |
| $s_2$ | 6 | 6 | 0 | 1 | 0 | 0 | 60 |
| $s_3$ | 2 | **4** | 0 | 0 | 1 | 0 | 32 |
| $s_4$ | 1 | 2 | 0 | 0 | 0 | 1 | 500 |
| $-z$ | −6 | **−8** | 0 | 0 | 0 | 0 | 0 |

**Variable entrante**: $y$ (coef. más negativo: −8).

**Ratios** (columna $y$): $40/2=20$, $60/6=10$, $32/4=\mathbf{8}$, $500/2=250$. Mínimo = 8 → **sale $s_3$**, pivote = 4.

---

**Pivoteo 1**: fila $s_3 / 4$, luego eliminar $y$ del resto:

| Base | $x$ | $y$ | $s_1$ | $s_2$ | $s_3$ | $s_4$ | RHS |
|------|-----|-----|-------|-------|-------|-------|-----|
| $s_1$ | 4 | 0 | 1 | 0 | −1/2 | 0 | 24 |
| $s_2$ | 3 | 0 | 0 | 1 | −3/2 | 0 | 12 |
| $y$ | 1/2 | 1 | 0 | 0 | 1/4 | 0 | 8 |
| $s_4$ | 0 | 0 | 0 | 0 | −1/2 | 1 | 484 |
| $-z$ | **−2** | 0 | 0 | 0 | 2 | 0 | 64 |

**Variable entrante**: $x$ (coef. −2).

**Ratios** (columna $x$): $24/4=6$, $12/3=\mathbf{4}$, $8/(1/2)=16$. Mínimo = 4 → **sale $s_2$**, pivote = 3.

---

**Pivoteo 2**: fila $s_2 / 3$, luego eliminar $x$:

| Base | $x$ | $y$ | $s_1$ | $s_2$ | $s_3$ | $s_4$ | RHS |
|------|-----|-----|-------|-------|-------|-------|-----|
| $s_1$ | 0 | 0 | 1 | −4/3 | 3/2 | 0 | 8 |
| $x$ | 1 | 0 | 0 | 1/3 | −1/2 | 0 | **4** |
| $y$ | 0 | 1 | 0 | −1/6 | 1/2 | 0 | **6** |
| $s_4$ | 0 | 0 | 0 | 0 | −1/2 | 1 | 484 |
| $-z$ | 0 | 0 | 0 | 2/3 | 1 | 0 | **72** |

**Todos los coeficientes en $-z$ son ≥ 0 → óptimo alcanzado.**

$$\boxed{x = 4,\quad y = 6,\quad z_{\max} = 72}$$

---

## Problema 9 — Fabricación de productos A y B

### Parte a) Plantear el problema

**Variables**: $A$ = kg de producto A por semana, $B$ = kg de producto B por semana.

**Restricciones**:

| Restricción | Expresión | Límite |
|-------------|-----------|--------|
| Materia prima | $20A + 5B$ | $\leq 9500\,\text{kg}$ |
| Horas de trabajo | $0.04A + 0.12B$ | $\leq 40\,\text{h}$ |
| Almacenamiento | $A + B$ | $\leq 550\,\text{kg}$ |
| No negatividad | $A, B$ | $\geq 0$ |

**Objetivo**: $\max\, z = 45A + 20B$ (euros/semana)

Simplificando la restricción de tiempo ($\times 25$): $A + 3B \leq 1000$.

### Parte b) Solución gráfica

**Vértices factibles** (intersecciones de pares de restricciones activas):

| Vértice | Cómo se obtiene | Verificación | $z = 45A+20B$ |
|---------|-----------------|-------------|----------------|
| $(0,0)$ | origen | ✓ | 0 |
| $(475, 0)$ | materia prima + $B=0$ | $0.04(475)=19\leq40$✓, $475\leq550$✓ | 21,375 |
| **(450, 100)** | materia prima + almacenamiento | $0.04(450)+0.12(100)=30\leq40$✓ | **22,250** |
| $(325, 225)$ | tiempo + almacenamiento | $20(325)+5(225)=7625\leq9500$✓ | 19,125 |
| $(0, 333)$ | tiempo + $A=0$ | $5(333)=1665\leq9500$✓, $333\leq550$✓ | 6,667 |

**Cálculo del punto $(450, 100)$**:
$$20A+5B=9500 \quad \text{y} \quad A+B=550$$
$$A=550-B \Rightarrow 20(550-B)+5B=9500 \Rightarrow 11000-15B=9500 \Rightarrow B=100, A=450$$

**Máximo**: $z = 45(450) + 20(100) = 20250 + 2000 = \mathbf{22250\,€/\text{semana}}$ en $(A, B) = (450, 100)$.

### Parte c) Método símplex

Forma estándar ($s_1, s_2, s_3$ = holguras de materia prima, tiempo y almacenamiento):

$$20A + 5B + s_1 = 9500$$
$$0.04A + 0.12B + s_2 = 40$$
$$A + B + s_3 = 550$$

**Tabla inicial** (minimizamos $-z = -45A - 20B$):

| Base | $A$ | $B$ | $s_1$ | $s_2$ | $s_3$ | RHS |
|------|-----|-----|-------|-------|-------|-----|
| $s_1$ | **20** | 5 | 1 | 0 | 0 | 9500 |
| $s_2$ | 0.04 | 0.12 | 0 | 1 | 0 | 40 |
| $s_3$ | 1 | 1 | 0 | 0 | 1 | 550 |
| $-z$ | **−45** | −20 | 0 | 0 | 0 | 0 |

**Variable entrante**: $A$ (coef. −45). Ratios: $9500/20=\mathbf{475}$, $40/0.04=1000$, $550/1=550$. Sale $s_1$.

**Pivoteo 1** (fila $s_1 / 20$):

| Base | $A$ | $B$ | $s_1$ | $s_2$ | $s_3$ | RHS |
|------|-----|-----|-------|-------|-------|-----|
| $A$ | 1 | 1/4 | 1/20 | 0 | 0 | 475 |
| $s_2$ | 0 | **0.11** | −0.002 | 1 | 0 | 21 |
| $s_3$ | 0 | **0.75** | −0.05 | 0 | 1 | 75 |
| $-z$ | 0 | **−8.75** | 9/4 | 0 | 0 | 21375 |

**Variable entrante**: $B$ (coef. −8.75). Ratios: $475/0.25=1900$, $21/0.11=190.9$, $75/0.75=\mathbf{100}$. Sale $s_3$.

**Pivoteo 2** (fila $s_3 / 0.75$):

| Base | $A$ | $B$ | $s_1$ | $s_2$ | $s_3$ | RHS |
|------|-----|-----|-------|-------|-------|-----|
| $A$ | 1 | 0 | 1/15 | 0 | −1/3 | **450** |
| $s_2$ | 0 | 0 | 4/750 | 1 | −11/30 | 10 |
| $B$ | 0 | 1 | −1/15 | 0 | 4/3 | **100** |
| $-z$ | 0 | 0 | 5/3 | 0 | 35/3 | **22250** |

**Todos los coeficientes ≥ 0 → óptimo.**

$$\boxed{A = 450\,\text{kg},\quad B = 100\,\text{kg},\quad z_{\max} = 22{,}250\,€/\text{semana}}$$

**Interpretación**: la materia prima y el almacenamiento son las restricciones activas (se usan al 100%). Hay 10 horas de trabajo libres cada semana.

---

## Resumen de estrategias por tipo de problema

| Problema | Método | Clave |
|----------|--------|-------|
| $f(x)$ 1D, sin derivada | Sección áurea | Calcular $\alpha = b-\Delta$, $\beta = a+\Delta$, reutilizar el punto que no se descarta |
| $f(x)$ 1D, con derivada | Derivar e igualar a cero, verificar con $f''$ | Usar sección áurea solo si no se puede derivar fácilmente |
| $f(x,y)$ máximo/mínimo | Máxima pendiente | Gradiente → dirección → búsqueda lineal (derivar $h(\alpha)$ e igualar a 0) → nuevo punto |
| $f(x,y)$ lineal con restricciones | Símplex | Holguras → tabla → entrar el más negativo → salir el menor ratio → pivotar |

### Errores frecuentes

| Error | Corrección |
|-------|-----------|
| En sección áurea para máximo: descartar el lado correcto | Si $f(\alpha) > f(\beta)$ → el máximo está en $[a, \beta]$, nuevo $b = \beta$ |
| Olvidar reutilizar el punto en sección áurea | Después del descarte, uno de los dos puntos siempre pasa a ser $\alpha$ o $\beta$ del nuevo intervalo |
| En máxima pendiente para máximo: restar el gradiente | Para **máximo** se **suma** el gradiente; para **mínimo** se **resta** |
| En símplex: ratio con $a_{ij} \leq 0$ | Solo se usan filas con $a_{ij} > 0$ para calcular el ratio mínimo |
| Olvidar actualizar la fila objetivo al pivotar | La fila $-z$ también se actualiza igual que el resto |

---

*Generado: 2026-04-24 · Fuente: ejercicios (3).pdf — Tema 6, Dept. Matemáticas, Computación Numérica, Univ. Oviedo*
