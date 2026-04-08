---
title: Aritmética Finita y Análisis del Error
description: "Cómo representan los ordenadores los números reales, qué tipos de error existen y cómo se propagan en los cálculos."
date: 2026-04-08
type: topic
subject: Computación Numérica
sources: [t1-aritmetica-finita.md]
tags: [error, aritmetica-finita, ieee-754, cifras-significativas, computacion-numerica, universidad]
---

# Aritmética Finita y Análisis del Error

## ¿Por qué existen errores en los cálculos numéricos?

Los ordenadores trabajan con un número **finito** de bits. Esto significa que la mayoría de los números reales **no se pueden representar exactamente**: solo existe una cuadrícula discreta de "números máquina". Cualquier número real que no caiga exactamente en esa cuadrícula introduce un error.

Además, existen otras fuentes de error:

| Tipo de error | Causa | Ejemplo |
|--------------|-------|---------|
| **Error de redondeo** | La máquina no puede representar el número exacto | $\pi$ se almacena como $3.14159265358979...$ cortado |
| **Error de truncamiento** | El método usa una aproximación (serie truncada, diferencias finitas...) | Aproximar $e^x$ por $1 + x + x^2/2$ |
| **Error en los datos** | Los datos de entrada ya son medidas aproximadas | Una constante experimental con $\pm 1\%$ de error |

---

## Conceptos de error

### Error absoluto y relativo

Sea $x$ el valor exacto y $\tilde{x}$ su aproximación:

$$\text{Error absoluto} = |x - \tilde{x}|$$

$$\text{Error relativo} = \frac{|x - \tilde{x}|}{|x|} \quad (x \neq 0)$$

> [!example] Ejemplo intuitivo
> Aproximar $x = 1$ por $\tilde{x} = 2$: error absoluto $= 1$, error relativo $= 100\%$.  
> Aproximar $y = 1000$ por $\tilde{y} = 1001$: error absoluto $= 1$, error relativo $= 0.1\%$.  
> El mismo error absoluto puede ser insignificante o catastrófico dependiendo de la magnitud del número.

### Cifras significativas

Se dice que $\tilde{x}$ aproxima a $x$ con **$k$ cifras significativas** si:

$$0.5 \times 10^{-k} < \text{error relativo} \leq 5 \times 10^{-k}$$

> [!example] Ejemplo
> ¿Con cuántas cifras significativas aproxima $200$ a $199$?
> Error relativo $= 1/199 \approx 0.00503$. Como $0.005 < 0.00503 \leq 0.05$, aproxima con **2 cifras significativas**.

---

## Representación de números en el ordenador

### Formato IEEE-754 (doble precisión)

El estándar IEEE-754 en doble precisión usa **64 bits**:

```
[ s ][ e₁e₂...e₁₁ ][ m₁m₂...m₅₂ ]
  ↑        ↑               ↑
 signo  exponente (11 bits)  mantisa (52 bits)
```

- **s**: 1 bit de signo (0 = positivo, 1 = negativo)
- **e**: 11 bits para el exponente (con sesgo)
- **m**: 52 bits para la mantisa (la parte decimal del número)

### Representación decimal normalizada

La forma habitual en teoría es el **formato decimal normalizado**:

$$\pm 0.d_1 d_2 \cdots d_k \times 10^n$$

donde $d_1 \neq 0$ (el primer dígito no es cero), $k$ es el número de dígitos de precisión de la máquina, y $n$ es el exponente entero.

> [!example] Ejemplo: aritmética de 2 dígitos
> Con $k = 2$ dígitos, los números máquina cerca de 1 son: $0.10 \times 10^1 = 1.0$, $0.11 \times 10^1 = 1.1$, $0.12 \times 10^1 = 1.2$, ...
> La distancia entre dos números máquina consecutivos en el intervalo $[10^n, 10^{n+1})$ es $10^{-k} \times 10^n$.

### Redondeo vs Truncamiento

Dado un número real $x = \pm 0.d_1 d_2 \cdots d_k d_{k+1} \cdots \times 10^n$:

- **Truncamiento**: tomar solo los $k$ primeros dígitos → $\tilde{x} = \pm 0.d_1 d_2 \cdots d_k \times 10^n$
- **Redondeo**: si $d_{k+1} \geq 5$, sumar 1 a $d_k$; si $d_{k+1} < 5$, truncar

> [!tip] Teorema importante
> Si $\tilde{x}$ es la aproximación por redondeo de $x$ con $k$ dígitos, entonces $\tilde{x}$ aproxima a $x$ con **al menos $k$ cifras significativas**.

---

## Análisis del error en operaciones

### El problema de la cancelación catastrófica

Cuando se restan dos números muy próximos entre sí, se pierden muchas cifras significativas. Esto se llama **cancelación catastrófica** y es uno de los principales peligros del cálculo numérico.

> [!warning] Ejemplo: ecuación de segundo grado
> Para $ax^2 + bx + c = 0$ con $a=1, b=62.10, c=1$ (soluciones exactas: $x_1 \approx -0.01611$, $x_2 \approx -62.08$):
>
> La fórmula estándar $x_1 = \frac{-b + \sqrt{b^2 - 4ac}}{2a}$ calcula $\frac{-62.10 + \sqrt{3856.41 - 4}}{2}$. Con 4 dígitos, $\sqrt{3852.41} \approx 62.07$ y entonces $-62.10 + 62.07 = -0.03$ → solo **1 cifra significativa** correcta.
>
> La fórmula alternativa $x_1 = \frac{-2c}{b + \sqrt{b^2 - 4ac}}$ evita la resta de números casi iguales y da un resultado mucho más preciso.

**Regla práctica:** cuando sea posible, reescribir las fórmulas para evitar restar números muy parecidos.

### Propiedad asociativa — no siempre se cumple

Con aritmética de precisión finita, la suma **no es siempre asociativa**:

> [!example]
> Con 3 dígitos y $x = -1000$, $y = 1000$, $z = 1$:
> - $(x + y) + z = 0 + 1 = 1$
> - $x + (y + z) = -1000 + 1001 = 1000$ → redondeado a 3 dígitos: $1000$, que en la suma con $-1000$ da $0$
>
> El resultado depende del orden de las operaciones.

---

## Métodos directos vs iterativos

| | Métodos directos | Métodos iterativos |
|--|-----------------|-------------------|
| **Cómo funcionan** | Número finito de operaciones → solución exacta (en teoría) | Generan sucesión $\{x_n\}$ con $\lim_{n \to \infty} x_n = x$ |
| **Error** | Solo error de redondeo | Error de redondeo + error de truncamiento |
| **Ejemplo** | Gauss, LU | Newton-Raphson, Jacobi |
| **Cuándo usar** | Problemas pequeños, solución exacta necesaria | Problemas grandes, solución aproximada suficiente |

---

## Convergencia de un método iterativo

Dado un método iterativo que genera $\{x_n\}$ con $x_n \to r$, el **error absoluto** es $e_n = |x_n - r|$.

El método converge si $e_n \to 0$.

La **velocidad de convergencia** importa mucho:

$$\lim_{n \to \infty} \frac{e_{n+1}}{e_n^p} = L$$

- Si $p = 1$ → **convergencia lineal** (lenta: cada iteración gana un porcentaje fijo)
- Si $p = 2$ → **convergencia cuadrática** (rápida: el número de cifras significativas se duplica en cada iteración)

---

## Related Topics

- [[Ecuaciones-No-Lineales]] — donde los errores de redondeo se vuelven críticos
- [[Metodos-Directos-Sistemas]] — número de condición: cuánto amplifica los errores un sistema lineal
- [[Interpolacion-Polinomial]] — error de truncamiento en interpolación
