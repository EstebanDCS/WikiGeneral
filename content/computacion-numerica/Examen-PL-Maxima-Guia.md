---
title: Guía Examen PL Global — Maxima/wxMaxima
type: output
output_type: study-guide
date: 2026-04-24
subject: Computación Numérica
based_on: [final2026.pdf, Sesiones-PL-1-8]
tags: [computacion-numerica, maxima, examen, practica, programacion]
---

# Guía Examen PL Global — Maxima/wxMaxima

## Estructura del examen (1h 40min)

| Ejercicio | Estado | Página del PDF |
|-----------|--------|---------------|
| **Ej 1** — `bisec()` + √3 con 150 dígitos | ✅ **FIJO siempre** | Página 1 |
| **Ej 8** — Matriz M(n) + sistemas lineales | ✅ **FIJO siempre** | Página 2 |
| **Ej 15** — Interpolación de Lagrange | ✅ **FIJO siempre** | Página 4 |
| **Ej 2** — Newton-Raphson | ⭐ **TÚ ELIGES** (más fácil de pág. 1) | Página 1 |
| **Ej 20** — Trapecio compuesto | ⭐ **TÚ ELIGES** (más fácil de pág. 5) | Página 5 |

> **Regla del examen**: los 2 que eliges deben venir de **páginas distintas**. El Ej 2 es de página 1 (distinta de pág 5 del Ej 20). ✓
>
> **Por qué estos dos**: Ej 2 reutiliza exactamente la misma función del Ej 1 (solo añades la derivada). Ej 20 es aplicar mecánicamente una fórmula en un bucle — no hay diseño de algoritmo.

---

## Lo primero que escribes en cualquier examen

```maxima
kill(all)$
fpprec: 160$
```

Siempre. Sin esto, `bfloat` no funciona bien y los resultados de alta precisión dan basura.

---

## Ejercicio 1 (FIJO) — `bisec()` + √3 con 150 dígitos decimales

### ¿Qué pide?

Construir la función `bisec(fu, a, b, tol)` (iterativa o recursiva) que implemente bisección. Luego usarla con $f(x) = x^2 - 3$ para obtener √3 con 150 cifras decimales correctas. Verificar el resultado.

### Por qué funciona

El método de bisección parte de un intervalo $[a,b]$ donde $f(a)$ y $f(b)$ tienen signos opuestos (garantía de Bolzano). En cada paso divide el intervalo por la mitad y se queda con la mitad donde sigue habiendo cambio de signo. El error se divide por 2 en cada iteración.

Para 150 dígitos decimales correctos necesitamos una tolerancia de $10^{-150}$. Con `bfloat` y `fpprec: 160` tenemos suficiente precisión.

### Código completo — versión iterativa

```maxima
kill(all)$
fpprec: 160$

bisec(fu, a, b, tol) := block(
    [c, fa, fb, fc],
    fa: fu(a),
    fb: fu(b),
    /* Verificar Bolzano: debe haber cambio de signo */
    if fa * fb > 0 then (
        print("ERROR: f(a) y f(b) tienen el mismo signo, no hay raíz garantizada"),
        return(false)
    ),
    /* Iterar: dividir el intervalo por la mitad */
    while (b - a) / 2 > tol do (
        c: (a + b) / 2,          /* punto medio */
        fc: fu(c),
        if fc = 0 then return(c), /* raíz exacta (raro pero posible) */
        if fa * fc < 0 then (     /* la raíz está en [a, c] */
            b: c,
            fb: fc
        ) else (                  /* la raíz está en [c, b] */
            a: c,
            fa: fc
        )
    ),
    (a + b) / 2                   /* retorna el punto medio del intervalo final */
)$

/* Definir f(x) = x^2 - 3 con bfloat para alta precisión */
define(f(x), x^2 - bfloat(3))$

/* Calcular √3 con 150 cifras decimales */
/* Tolerancia: 10^(-155) para asegurar 150 dígitos correctos */
raiz3: bisec(f, bfloat(1), bfloat(2), bfloat(10)^(-155))$

/* Mostrar resultado */
print("√3 ≈", raiz3)$

/* VERIFICACIÓN: elevar al cuadrado y restar 3, debe ser ~0 */
print("Verificación (raiz3^2 - 3):", raiz3^2 - bfloat(3))$
```

### Código completo — versión recursiva (alternativa válida)

```maxima
bisec(fu, a, b, tol) := block(
    [c: (a + b) / 2, fc],
    /* Caso base: intervalo suficientemente pequeño */
    if (b - a) / 2 < tol then return(c),
    fc: fu(c),
    if fc = 0 then return(c),
    /* Llamada recursiva al subintervalo correcto */
    if fu(a) * fc < 0 then
        bisec(fu, a, c, tol)
    else
        bisec(fu, c, b, tol)
)$
```

> **Truco crítico**: los extremos del intervalo deben ser `bfloat(1)` y `bfloat(2)`, NO `1` y `2`. Si pasas enteros, Maxima hace aritmética entera y pierde la alta precisión aunque `fpprec` esté a 160.

### ¿Cuántas iteraciones necesita?

$$N \geq \frac{\ln(b-a) + 155 \cdot \ln(10)}{\ln 2} \approx \frac{0 + 355.7}{0.693} \approx 513 \text{ iteraciones}$$

Bisección es lenta pero segura. Newton-Raphson (Ej 2) converge en ~10 iteraciones para el mismo resultado.

---

## Ejercicio 2 (TÚ ELIGES — Página 1) — Newton-Raphson

### ¿Qué pide?

Programar `newton(fu, x0, tol)` siguiendo el esqueleto que da el profesor:
```
newton(fu,x0,tol) := block(define(dfu(x),diff(fu(x),x,1)), while...., x0)
```
Y probarlo con la misma $f(x) = x^2 - 3$ del ejercicio 1.

### Por qué es fácil

Es casi idéntico al Ej 1. La diferencia es que en vez de dividir el intervalo por la mitad, calculas el siguiente punto usando la fórmula:
$$x_{n+1} = x_n - \frac{f(x_n)}{f'(x_n)}$$

La derivada se calcula automáticamente con `diff`. Converge en ~10 iteraciones en vez de ~500.

### Código completo

```maxima
kill(all)$
fpprec: 160$

newton(fu, x0, tol) := block(
    [x: x0, xnew],
    /* El profesor pide esta línea: define la derivada dentro del block */
    define(dfu(t), diff(fu(t), t, 1)),
    /* Iterar: fórmula de Newton-Raphson */
    while true do (
        /* Guardar contra derivada nula (dividiría entre cero) */
        if abs(dfu(x)) < bfloat(10)^(-200) then (
            print("Derivada nula en x =", x),
            return(false)
        ),
        xnew: x - fu(x) / dfu(x),        /* fórmula NR */
        if abs(xnew - x) < tol then return(xnew),  /* convergió */
        x: xnew                            /* actualizar punto */
    )
)$

/* Misma función que en Ejercicio 1 */
define(f(x), x^2 - bfloat(3))$

/* Calcular √3 — punto de inicio x0 = 1.5 (cerca de la raíz) */
raiz3_nr: newton(f, bfloat(3)/2, bfloat(10)^(-155))$

print("√3 por NR:", raiz3_nr)$
print("Verificación:", raiz3_nr^2 - bfloat(3))$
```

### ¿Por qué converge tan rápido?

Newton-Raphson tiene convergencia **cuadrática**: el número de dígitos correctos se duplica en cada iteración.

| Iteración | Dígitos correctos |
|-----------|------------------|
| 1 | ~2 |
| 2 | ~4 |
| 3 | ~8 |
| 4 | ~16 |
| 10 | ~512 |

vs. bisección: 1 dígito nuevo cada ~3.3 iteraciones.

### Comparación directa Ej 1 vs Ej 2

```maxima
/* Ambos hacen lo mismo: encontrar raíz de f(x)=0 */

/* Bisección: garantiza convergencia si hay cambio de signo */
raiz_b: bisec(f, bfloat(1), bfloat(2), bfloat(10)^(-155))$

/* Newton-Raphson: converge más rápido si x0 está cerca de la raíz */
raiz_n: newton(f, bfloat(3)/2, bfloat(10)^(-155))$

/* Deben ser (prácticamente) iguales */
print("Diferencia entre métodos:", abs(raiz_b - raiz_n))$
```

---

## Ejercicio 8 (FIJO) — Matriz M(n) y sistemas lineales

### ¿Qué pide?

1. Crear `M(n)`: matriz $n\times n$ con 11 en la diagonal, 1 donde $|i-j| < 3$, 0 en otro caso.
2. Crear `b(n)`: vector columna de $n$ unos.
3. Resolver $M(7)\cdot X = b(7)$ de **dos maneras**:
   - Usando la **inversa**: $X = M(7)^{-1} \cdot b(7)$
   - Usando **echelon** + sustitución regresiva
4. Indicar la suma $x_1 + x_2 + \cdots + x_7$ (número racional exacto).

### Código completo

```maxima
kill(all)$

/* ── Paso 1: Definir M(n) ───────────────────────────────────────── */
M(n) := block(
    [A],
    A: zeromatrix(n, n),          /* empezar con todo ceros */
    for i:1 thru n do
        for j:1 thru n do (
            if i = j then
                A[i][j]: 11       /* diagonal: 11 */
            else if abs(i - j) < 3 then
                A[i][j]: 1        /* vecinos cercanos: 1 */
            /* else: queda 0, ya está */
        ),
    A                             /* retorna la matriz */
)$

/* ── Paso 2: Definir b(n) ───────────────────────────────────────── */
b(n) := transpose(matrix(makelist(1, i, 1, n)))$
/* b(7) es un vector columna 7×1 de unos */

/* ── Verificar que las definiciones son correctas ───────────────── */
print("M(7) =")$
print(M(7))$
print("b(7) =")$
print(b(7))$

/* ══ MÉTODO 1: por la inversa ════════════════════════════════════ */
/* X = M(7)^{-1} · b(7)   ← usar . para producto matricial */
X1: invert(M(7)) . b(7)$
print("Solución por inversa:")$
print(X1)$

/* Suma de todas las incógnitas (número racional) */
suma1: sum(X1[i][1], i, 1, 7)$
print("x1 + x2 + ... + x7 =", suma1)$

/* ══ MÉTODO 2: echelon + sustitución regresiva ═══════════════════ */
/* Crear matriz aumentada [M(7) | b(7)] */
Aug: addcol(M(7), b(7))$
print("Matriz aumentada [M|b] =")$
print(Aug)$

/* Reducir a forma escalonada */
E: echelon(Aug)$
print("Forma escalonada:")$
print(E)$

/* Sustitución regresiva: resolver de abajo arriba */
back_sub(E, n) := block(
    [x, i, j, s],
    x: makelist(0, k, 1, n),
    for i:n thru 1 step -1 do (     /* desde la última fila hacia arriba */
        s: E[i][n+1],                /* término independiente */
        for j:i+1 thru n do
            s: s - E[i][j] * x[j],  /* restar los ya calculados */
        x[i]: ratsimp(s / E[i][i])  /* despejar x[i] */
    ),
    x
)$

X2: back_sub(E, 7)$
print("Solución por echelon:")$
print(X2)$

suma2: sum(X2[i], i, 1, 7)$
print("x1 + x2 + ... + x7 =", suma2)$

/* ── Verificación: ambos métodos deben dar el mismo resultado ─── */
print("¿Soluciones iguales?", ratsimp(X1[1][1] - X2[1]) = 0)$
```

### Qué aspecto tiene M(7)

```
M(7) = 
( 11  1  1  0  0  0  0 )
(  1 11  1  1  0  0  0 )
(  1  1 11  1  1  0  0 )
(  0  1  1 11  1  1  0 )
(  0  0  1  1 11  1  1 )
(  0  0  0  1  1 11  1 )
(  0  0  0  0  1  1 11 )
```

La diagonal vale 11. Los elementos con $|i-j|=1$ (vecinos inmediatos) valen 1. Los con $|i-j|=2$ también valen 1. El resto son 0.

### Errores típicos en este ejercicio

| Error | Solución |
|-------|---------|
| Usar `*` para multiplicar matrices | Usar `.` (punto): `invert(M(7)) . b(7)` |
| `X1[i]` en vez de `X1[i][1]` | El resultado de `invert(A).b` es una matriz columna, los elementos son `X1[i][1]` |
| `echelon` devuelve fracciones raras | Normal, es la forma racional exacta |

---

## Ejercicio 15 (FIJO) — Interpolación de Lagrange

### ¿Qué pide?

Definir `Lagrange(fun, nodos)` que devuelva el polinomio interpolador de `fun` en los nodos dados. Probarla con un polinomio de grado 6 y 7 nodos (debe recuperar exactamente el polinomio original).

### La fórmula de Lagrange

$$P(x) = \sum_{i=1}^{n} f(\text{nodos}_i) \cdot \underbrace{\prod_{j=1, j\neq i}^{n} \frac{x - \text{nodos}_j}{\text{nodos}_i - \text{nodos}_j}}_{L_i(x)}$$

Cada $L_i(x)$ vale 1 en el nodo $i$ y 0 en todos los demás. El producto final es el polinomio que pasa exactamente por todos los puntos.

### Código completo

```maxima
kill(all)$

/* ── Definir la función Lagrange ──────────────────────────────── */
Lagrange(fun, nodos) := expand(        /* expand simplifica el polinomio */
    sum(
        fun(nodos[i]) *                /* valor de la función en el nodo i */
        product(
            (x - nodos[j]) / (nodos[i] - nodos[j]),  /* factor L_i */
            j, 1, length(nodos),
            j # i                      /* j distinto de i */
        ),
        i, 1, length(nodos)
    )
)$

/* ── TEST: polinomio de grado 6, 7 nodos ─────────────────────── */

/* Función de prueba: cualquier polinomio de grado 6 funciona */
define(p(x), x^6 - 3*x^5 + 2*x^4 - x^3 + 4*x^2 - x + 1)$

/* 7 nodos equiespaciados en [-3, 3] */
nodos7: makelist(-3 + k, k, 0, 6)$
/* = [-3, -2, -1, 0, 1, 2, 3] */
print("Nodos:", nodos7)$

/* Calcular polinomio de Lagrange */
P: Lagrange(p, nodos7)$
print("Polinomio interpolante P(x) =", P)$

/* VERIFICACIÓN: P(x) debe ser igual a p(x) */
/* Si la diferencia es 0, la función es correcta */
diferencia: expand(P - p(x))$
print("P(x) - p(x) =", diferencia, " (debe ser 0)")$

/* Verificar también en un punto concreto */
print("P(0.5) =", float(subst(x=0.5, P)))$
print("p(0.5) =", float(p(0.5)))$
print("Error puntual:", abs(float(subst(x=0.5,P)) - p(0.5)))$
```

### Por qué usar `expand`

Sin `expand`, el resultado es el producto de factores no simplificado. Con `expand`, Maxima lo convierte en $a_6 x^6 + a_5 x^5 + \cdots + a_0$, que es reconocible y comparable con el polinomio original.

### ¿Qué pasa si cambias los nodos?

```maxima
/* Nodos distintos: puntos no equiespaciados */
nodos_alt: [-3, -1, 0, 1/2, 1, 2, 3]$
P2: Lagrange(p, nodos_alt)$
expand(P2 - p(x))$  /* sigue siendo 0: no depende de los nodos para polinomios */
```

Para un polinomio de grado $n$ con $n+1$ nodos, el interpolante es siempre exacto independientemente de dónde estén los nodos.

---

## Ejercicio 20 (TÚ ELIGES — Página 5) — Trapecio compuesto con $n = 10^5$

### ¿Qué pide?

Calcular $\displaystyle\int_0^2 \left(\frac{x^5}{60} - \frac{x^6}{360} - x^2\right) dx$ usando el **método del trapecio compuesto** con $n = 10^5$ subintervalos. Indicar el error absoluto y comprobar que es menor que la cota teórica.

### Por qué es fácil

No hay que diseñar ningún algoritmo inteligente. Solo aplicar la fórmula:

$$T_n = \frac{h}{2}\left[f(x_0) + 2f(x_1) + 2f(x_2) + \cdots + 2f(x_{n-1}) + f(x_n)\right], \quad h = \frac{b-a}{n}$$

Es una suma con patrón $1, 2, 2, \ldots, 2, 1$ multiplicada por $h/2$.

### La cota de error teórica

$$|E_T| \leq \frac{(b-a)^3 \cdot M_2}{12\,n^2}, \quad M_2 = \max_{x \in [0,2]}|f''(x)|$$

### Calcular $M_2$ antes del código

$f(x) = \dfrac{x^5}{60} - \dfrac{x^6}{360} - x^2$

$f'(x) = \dfrac{x^4}{12} - \dfrac{x^5}{60} - 2x$

$f''(x) = \dfrac{x^3}{3} - \dfrac{x^4}{12} - 2$

En $[0, 2]$: $f''(0) = -2$, $f''(2) = \frac{8}{3} - \frac{16}{12} - 2 = \frac{8}{3} - \frac{4}{3} - 2 = \frac{4}{3} - 2 = -\frac{2}{3}$

Como $f'''(x) = x^2 - x^3/3 \geq 0$ para $x \in [0,2]$, $f''$ es creciente → el máximo de $|f''|$ está en $x=0$: $M_2 = 2$.

Cota: $\dfrac{(2)^3 \cdot 2}{12 \cdot (10^5)^2} = \dfrac{16}{12 \times 10^{10}} \approx 1.33 \times 10^{-10}$

### Código completo

```maxima
kill(all)$

/* ── Definir la función ────────────────────────────────────────── */
define(g(x), x^5/60 - x^6/360 - x^2)$

/* ── Valor exacto (Maxima lo calcula simbólicamente) ──────────── */
exact: integrate(g(x), x, 0, 2)$
print("Integral exacta:", exact, "=", float(exact))$
/* Resultado: exact = -160/63 ≈ -2.5397 */

/* ── Trapecio compuesto con n = 100000 ───────────────────────── */
n: 100000$
a: 0.0$          /* usar decimales (float) para que el bucle sea rápido */
b: 2.0$
h: (b - a) / n$

/* Suma con patrón: f(a) + 2*f(x1) + 2*f(x2) + ... + 2*f(x_{n-1}) + f(b) */
S: g(a) + g(b)$                        /* los extremos cuentan 1 vez */
for k:1 thru n-1 do
    S: S + 2.0 * g(a + k * h)$         /* los interiores cuentan 2 veces */
T: h * S / 2.0$
print("Trapecio compuesto:", T)$

/* ── Error absoluto ───────────────────────────────────────────── */
error_abs: abs(T - float(exact))$
print("Error absoluto:", error_abs)$

/* ── Cota teórica ─────────────────────────────────────────────── */
M2: 2$     /* max|f''| en [0,2] = |f''(0)| = |-2| = 2 */
cota: float((b - a)^3 * M2 / (12 * n^2))$
print("Cota teórica:", cota)$

/* ── Verificación: ¿error < cota? ─────────────────────────────── */
print("¿Error absoluto < cota teórica?", error_abs < cota)$
/* Debe imprimir: true */
```

### Qué esperar de la salida

```
Integral exacta: -160/63 = -2.539682539...
Trapecio compuesto: -2.539682538...
Error absoluto: ~8.5e-11
Cota teórica: 1.33e-10
¿Error absoluto < cota teórica? true
```

### Por qué el bucle no es lento

Con `a: 0.0` y `b: 2.0` (decimales, no enteros), Maxima hace aritmética flotante en el bucle. Si pusieras `a: 0` y `b: 2` (enteros), haría aritmética simbólica — extremadamente lento para 100000 iteraciones.

---

## Referencia rápida — Maxima esencial

### Lo que más se usa en el examen

```maxima
/* Alta precisión */
fpprec: 160$
bfloat(expr)          /* evaluar con 160 dígitos */
bfloat(10)^(-155)     /* tolerancia de 155 dígitos */

/* Definir funciones */
define(f(x), x^2 - bfloat(3))$
define(dfu(t), diff(fu(t), t, 1))$   /* derivada automática */

/* Listas y matrices */
makelist(expr, i, a, b)               /* crear lista */
zeromatrix(n, n)                      /* matriz de ceros */
A[i][j]: valor$                       /* asignar elemento */

/* Operaciones matriciales */
invert(A)                             /* inversa */
A . B                                 /* producto matricial (punto, NO asterisco) */
addcol(A, b)                          /* añadir columna: [A|b] */
echelon(A)                            /* forma escalonada */
transpose(A)                          /* transpuesta */

/* Álgebra */
expand(expr)                          /* expandir (imprescindible en Lagrange) */
ratsimp(expr)                         /* simplificar fracción */
float(expr)                           /* convertir a decimal */
subst(x=val, expr)                    /* sustituir valor */
diff(f(x), x, n)                      /* derivada n-ésima */
integrate(f(x), x, a, b)             /* integral definida */

/* Control de flujo */
block([vars_locales], ..., retorno)   /* función con variables locales */
while condicion do (...)              /* bucle while */
for i:1 thru n do (...)              /* bucle for */
for i:n thru 1 step -1 do (...)      /* bucle for inverso */
if cond then ... else ...$            /* condicional */
return(valor)                         /* salir de block */
```

### Errores que cuestan puntos

| Error | Síntoma | Corrección |
|-------|---------|-----------|
| `A * B` para matrices | Resultado incorrecto silencioso | Usar `A . B` |
| Olvidar `bfloat()` en los extremos de `bisec` | Precisión de 15 dígitos aunque `fpprec=160` | `bisec(f, bfloat(1), bfloat(2), ...)` |
| `return()` fuera de `block` | No hace nada | Siempre dentro de `block([...], ...)` |
| Índice 0 en lista | Error "índice fuera de rango" | Maxima usa **base 1**: `L[1]` es el primer elemento |
| `X1[i]` en vez de `X1[i][1]` | Error de dimensión | El resultado de `invert(A).b` es una **matriz columna** |
| `sum(f(i),i,1,n)` con n=10^5 | Extremadamente lento | Usar bucle `for` con valores `float` |
| Olvidar `kill(all)` al inicio | Variables de sesión anterior interfieren | Siempre empezar con `kill(all)` |

---

## Lista completa de ejercicios del PDF (referencia)

| Ej | Contenido | Página | Dificultad |
|----|-----------|--------|-----------|
| **1** | `bisec()` + √3 con 150 dígitos | 1 | **FIJO** |
| 2 | Newton-Raphson | 1 | ⭐ Fácil |
| 3 | Método de la secante | 1 | Fácil |
| 4 | Punto fijo, $x = \cos(x)$, 108 dígitos | 1 | Media |
| 5 | Sucesión OEIS A226775 con bisección | 1 | Media |
| 6 | Punto fijo sobre la ec. del Ej 5 | 1 | Media |
| 7 | $T(x)=\sum_{k=1}^{1000}k^{-x}=2$, bisección con bfloat | 1 | Media |
| **8** | M(n) + sistemas con inversa y echelon | 2 | **FIJO** |
| 9 | Gauss-Seidel con M(500) | 2 | Media |
| 10 | Test de definida positiva | 2 | Media |
| 11 | Descomposición de Cholesky | 2 | Alta |
| 12 | Punto fijo multidimensional, sistema 3D | 3 | Media |
| 13 | Newton-Raphson para sistemas (Jacobiano) | 3 | Media-Alta |
| 14 | Multiplicadores de Lagrange + Geogebra | 3 | Alta |
| **15** | Interpolación de Lagrange | 4 | **FIJO** |
| 16 | Nodos de Chebyshev vs equiespaciados | 4 | Fácil |
| 17 | Raíz cuarta de una matriz | 4 | Alta |
| 18 | sin(M) y cos(M) de una matriz | 4 | Media |
| 19 | exp(A) via interpolación, verificar con serie | 4 | Alta |
| **20** | Trapecio compuesto, $n=10^5$, cota de error | 5 | ⭐ Fácil |
| 21 | Simpson compuesto, $n=10^5$, cota de error | 5 | Fácil |
| 22 | Área por Montecarlo (1 millón de simulaciones) | 5 | Media |
| 23 | Máximo/mínimo de $f(x)=e^{-x^2+x}\sin(x+\cos x)$ | 5 | Media |

> **Resumen de elección**: Ej 2 (página 1) + Ej 20 (página 5) son los más directos. Si prefieres variante: Ej 3 (página 1) + Ej 21 (página 5) es igual de fácil — Simpson es casi idéntico al trapecio.

---

*Actualizado: 2026-04-24 · Fuente: final2026.pdf (23 ejercicios) + Sesiones PL 1–8*
