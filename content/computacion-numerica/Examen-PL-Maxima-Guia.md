---
title: Guía Examen PL Global — Maxima/wxMaxima
type: output
output_type: study-guide
date: 2026-04-28
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
fpprec: 400$
```

**`fpprec: 400`**, no 160. El profesor usa 400 en sus sesiones para tener margen amplio. Con 160 técnicamente sería suficiente, pero si pones 400 nunca falla.

---

## Ejercicio 1 (FIJO) — `bisec()` + √3 con 150 dígitos decimales

### ¿Qué pide?

Construir `bisec(fu, a, b, tol)` e implementar bisección. Luego usarla con $f(x) = x^2 - 3$ para obtener √3 con 150 cifras decimales. Verificar el resultado.

### Hay dos versiones — el examen puede pedir cualquiera

---

### Versión iterativa (con `block`)

Esta es la versión estándar. El profesor la implementa con una función local `c(a,b)` dentro del `block`:

```maxima
kill(all)$
fpprec: 400$

bisec(fu, a, b, tol) := block(
    c(a,b) := (a+b)/2,                  /* función local para el punto medio */
    while abs(b-a) > tol do              /* OJO: abs(b-a), no (b-a)/2 */
        if fu(a)*fu(c(a,b)) < 0
        then b: c(a,b)
        else a: c(a,b),
    c(a,b)                               /* retorna el punto medio final */
)$

/* Definir f(x) = x² - 3 */
define(f(x), x^2 - bfloat(3))$

/* Llamada: argumentos ENTEROS EXACTOS, no bfloat() */
raiz3: bisec(f, 1, 2, 10^(-155))$

print("√3 ≈", bfloat(raiz3))$
print("Verificación (raiz²-3):", bfloat(raiz3^2 - 3))$
```

> **Nota**: el resultado sale en formato fracción (p.ej. `3715.../2145...`). Es correcto — es aritmética racional exacta. Para ver el decimal usa `bfloat(raiz3)`.

---

### Versión recursiva (sin `block`) ⚠️

**Trampa crítica**: si usas `block()` en la versión recursiva → **desbordamiento de pila** (~515 llamadas recursivas con block = crash en SBCL).

La versión recursiva correcta es **puro `if-then-else`, sin `block`, sin variables locales**:

```maxima
kill(all)$
fpprec: 400$

bisecR(fu, a, b, tol) :=
    if abs(b-a) < tol
    then (a+b)/2
    else if fu(a)*fu((a+b)/2) < 0
    then bisecR(fu, a, (a+b)/2, tol)
    else bisecR(fu, (a+b)/2, b, tol)$

define(f(x), x^2 - bfloat(3))$

/* Argumentos enteros exactos: 1 y 2, NO bfloat(1), bfloat(2) */
/* El profesor lo avisa explícitamente: "OJO, hay que usar números enteros o racionales" */
raiz3: bisecR(f, 1, 2, 10^(-155))$

print("√3 ≈", bfloat(raiz3))$
```

| | Iterativa | Recursiva |
|---|---|---|
| Usa `block()` | ✅ Sí | ❌ No (stack overflow) |
| Argumentos | Enteros exactos | Enteros exactos |
| Velocidad | Igual | Igual |
| Resultado | Fracción exacta | Fracción exacta |

### ¿Cuántas iteraciones necesita?

$$N \geq \frac{\ln(b-a) + 155 \cdot \ln(10)}{\ln 2} \approx 513 \text{ iteraciones}$$

Bisección es lenta pero segura. Newton-Raphson (Ej 2) converge en ~10 iteraciones para el mismo resultado.

---

## Ejercicio 2 (TÚ ELIGES — Página 1) — Newton-Raphson

### ¿Qué pide?

Programar `Newton(fu, x0, tol)`. El profesor da el esqueleto exacto en sus apuntes:

```
Newton(fu, x0, tol) := block(define(dfu(x), diff(fu(x),x,1)), while..., x0)
```

Y probarlo con la misma $f(x) = x^2 - 3$ del ejercicio 1.

### Código completo — firma exacta del profesor

```maxima
kill(all)$
fpprec: 400$

Newton(fu, x0, tol) := block(
    define(dfu(x), diff(fu(x), x, 1)),           /* derivada automática */
    while abs(fu(x0)) > tol do                    /* condición: |f(x0)| > tol */
        x0: bfloat(x0 - fu(x0)/dfu(x0)),         /* bfloat() en la ACTUALIZACIÓN */
    x0                                             /* retorna x0 (último valor del block) */
)$

/* Misma función que en Ej 1 */
define(f(x), x^2 - bfloat(3))$

/* Punto de inicio: cualquier valor razonable cerca de la raíz */
raiz3_nr: Newton(f, 1, 10^(-155))$

print("√3 por NR:", raiz3_nr)$
print("Verificación:", bfloat(raiz3_nr^2 - 3))$
```

> **Diferencias clave respecto a lo que se podría pensar**:
> - La condición de parada es `abs(fu(x0)) > tol`, **no** `abs(xnew - x) < tol`
> - El `bfloat()` va en la **actualización de x0**, no en los argumentos de entrada
> - Se actualiza `x0` directamente, sin variable auxiliar `xnew`
> - Al final del `block` se devuelve `x0` implícitamente (sin `return`)

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
raiz_b: bisecR(f, 1, 2, 10^(-155))$
raiz_n: Newton(f, 1, 10^(-155))$

/* Deben ser (prácticamente) iguales */
print("Diferencia entre métodos:", bfloat(abs(raiz_b - raiz_n)))$
```

---

## Ejercicio 8 (FIJO) — Matriz M(n) y sistemas lineales

### ¿Qué pide?

1. Crear `M(n)`: matriz $n\times n$ con 11 en la diagonal, 1 donde $|i-j| < 3$, 0 en otro caso.
2. Crear `b(n)`: vector de longitud $n$ (lista de unos, o vector columna).
3. Resolver $M(7)\cdot X = b(7)$ de **dos maneras**:
   - Usando la **inversa**: $X = M(7)^{-1} \cdot b(7)$
   - Usando **echelon** + sustitución regresiva
4. Indicar la suma $x_1 + x_2 + \cdots + x_7$.

### Código completo — estilo del profesor

```maxima
kill(all)$

/* ── Definir M(n) ─────────────────────────────────────────────────── */
/* El profesor define primero una función elemento a(n,i,j) y luego construye */

a8(i,j) := if i=j then 11
           else if abs(i-j) < 3 then 1
           else 0$

M(n) := apply('matrix, makelist(makelist(a8(i,j), j,1,n), i,1,n))$

/* ── Definir b(n) ─────────────────────────────────────────────────── */
b(n) := makelist(k*0+1, k, 1, n)$   /* lista de n unos */

/* ── Verificar ────────────────────────────────────────────────────── */
print("M(7) =")$
print(M(7))$
print("b(7) =", b(7))$

/* ══ MÉTODO 1: por la inversa ════════════════════════════════════════ */
X1: invert(M(7)) . transpose(matrix(b(7)))$
print("Solución por inversa:")$
print(X1)$
suma1: sum(X1[i][1], i, 1, 7)$
print("x1 + ... + x7 =", suma1)$

/* ══ MÉTODO 2: echelon + sustitución regresiva ═══════════════════════ */
/* El profesor usa esta forma de definir la sustitución con arrays */

AMPLI: addcol(M(7), transpose(matrix(b(7))))$
ESC: echelon(AMPLI)$
print("Forma escalonada:")$
print(ESC)$

/* Sustitución regresiva — estilo del profesor */
usandoechelon(MM, bb) := block(
    kill(X),
    dim: length(bb),
    AMPLI2: addcol(MM, transpose(matrix(bb))),
    ESC2: echelon(AMPLI2),
    X[i] := if i = dim
             then ESC2[dim, dim+1]
             else ESC2[i, dim+1] - sum(ESC2[i,k]*X[k], k, i+1, dim),
    makelist(x[i] = X[i], i, 1, dim)
)$

sol: usandoechelon(M(7), b(7))$
print("Solución por echelon:", sol)$
suma2: sum(rhs(sol[i]), i, 1, 7)$
print("x1 + ... + x7 =", suma2)$

/* ── Verificación ─────────────────────────────────────────────────── */
print("¿Ambos métodos coinciden?", ratsimp(suma1 - suma2) = 0)$
print("Verificación M·X1 - b (debe ser 0):", M(7) . X1 - transpose(matrix(b(7))))$
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

La diagonal vale 11. Los elementos con $|i-j| < 3$ (vecinos a distancia 1 o 2) valen 1. El resto son 0.

### Errores típicos en este ejercicio

| Error | Solución |
|-------|---------|
| Usar `*` para multiplicar matrices | Usar `.` (punto): `invert(M(7)) . b` |
| `X1[i]` en vez de `X1[i][1]` | El resultado de `invert(A).b` es una matriz columna |
| `echelon` devuelve fracciones raras | Normal, es la forma racional exacta |
| `b(n)` como lista vs matriz columna | Ojo con el formato — `transpose(matrix(b(7)))` lo convierte |

---

## Ejercicio 15 (FIJO) — Interpolación de Lagrange

### ¿Qué pide?

Definir `Lagrange(fun, nodos)` que devuelva el polinomio interpolador de `fun` en los nodos dados. Probarla con un polinomio de grado 6 y 7 nodos (debe recuperar exactamente el polinomio original).

### La fórmula de Lagrange

$$P(x) = \sum_{i=1}^{n} f(\text{nodos}_i) \cdot \prod_{\substack{j=1 \\ j\neq i}}^{n} \frac{x - \text{nodos}_j}{\text{nodos}_i - \text{nodos}_j}$$

### Código completo

```maxima
kill(all)$

Lagrange(fun, nodos) := expand(
    sum(
        fun(nodos[i]) *
        product(
            (x - nodos[j]) / (nodos[i] - nodos[j]),
            j, 1, length(nodos),
            j # i
        ),
        i, 1, length(nodos)
    )
)$

/* ── TEST: polinomio de grado 6, 7 nodos ─────────────────────────── */
define(p(x), x^6 - 2*x^5 + 3*x^4 - x^3 + x - 1)$

/* 7 nodos equiespaciados en [-1, 1]: [-1, -2/3, -1/3, 0, 1/3, 2/3, 1] */
nodos7: makelist(-1 + 2*i/6, i, 0, 6)$
print("Nodos:", nodos7)$

P: Lagrange(p, nodos7)$

/* VERIFICACIÓN: P(x) - p(x) debe ser 0 */
error15: expand(P - p(x))$
print("P(x) - p(x) =", error15, "(debe ser 0)")$

if error15 = 0
    then print("✅ Interpolación exacta")
    else print("❌ Error en Lagrange")$

print("P(0.5) =", float(subst(x=0.5, P)))$
print("p(0.5) =", float(p(0.5)))$
```

### Por qué usar `expand`

Sin `expand`, el resultado es el producto de factores no simplificado. Con `expand`, Maxima lo convierte en $a_6 x^6 + \cdots + a_0$, comparable con el polinomio original.

---

## Ejercicio 20 (TÚ ELIGES — Página 5) — Integración numérica compuesta

### ¿Qué pide?

Implementar `trapecio_comp(fu, a, b, n)` y/o `simpson_comp(fu, a, b, n)`. Aplicar para calcular una integral concreta con $n$ subintervalos, comparar con `quad_qags` y calcular el error.

### Fórmulas

**Trapecio compuesto** ($h = (b-a)/n$):
$$T_n = \frac{h}{2}\left[f(x_0) + 2f(x_1) + \cdots + 2f(x_{n-1}) + f(x_n)\right]$$

**Simpson compuesto** ($n$ par, $h = (b-a)/n$):
$$S_n = \frac{h}{3}\left[f(x_0) + 4f(x_1) + 2f(x_2) + 4f(x_3) + \cdots + 4f(x_{n-1}) + f(x_n)\right]$$

Patrón de coeficientes Simpson: $1, 4, 2, 4, 2, \ldots, 4, 1$

### Código completo

```maxima
kill(all)$

/* ── Trapecio compuesto ───────────────────────────────────────────── */
trapecio_comp(fu, a, b, n) := block(
    [h, s, i],
    h: float(b-a)/n,
    s: fu(a) + fu(b),                    /* extremos con coef. 1 */
    for i:1 thru n-1 do
        s: s + 2*fu(a + i*h),            /* interiores con coef. 2 */
    h/2 * s
)$

/* ── Simpson compuesto ────────────────────────────────────────────── */
simpson_comp(fu, a, b, n) := block(
    [h, s, i],
    h: float(b-a)/n,
    s: fu(a) + fu(b),                    /* extremos con coef. 1 */
    for i:1 thru n-1 do
        if oddp(i)
        then s: s + 4*fu(a + i*h)        /* impar → coef. 4 */
        else s: s + 2*fu(a + i*h),       /* par   → coef. 2 */
    h/3 * s
)$

/* ── Aplicación: ∫₀¹ sin(x²) dx con n=10 ─────────────────────────── */
define(g(x), sin(x^2))$

/* Valor de referencia con quad_qags */
I_exacta: quad_qags(g(x), x, 0, 1)[1]$   /* [1] extrae solo el valor numérico */

I_trap: trapecio_comp(g, 0, 1, 10)$
I_simp: simpson_comp(g, 0, 1, 10)$

print("quad_qags:  ", I_exacta)$
print("Trapecio:   ", I_trap)$
print("Simpson:    ", I_simp)$
print("Error trap: ", abs(I_trap - I_exacta))$
print("Error simp: ", abs(I_simp - I_exacta))$

/* Simpson debe ser ~100x más preciso (O(h⁴) vs O(h²)) */
if abs(I_simp - I_exacta) < abs(I_trap - I_exacta)
    then print("✅ Simpson más preciso que Trapecio")
    else print("❌ Revisar coeficientes")$
```

### Puntos clave

- `quad_qags(f(x), x, a, b)[1]` — el `[1]` es obligatorio, extrae el valor (el resto son códigos de error, etc.)
- `float(b-a)/n` — usar `float` para que el bucle sea rápido (aritmética decimal, no simbólica)
- `oddp(i)` devuelve `true` si `i` es impar — útil para los coeficientes de Simpson
- `n` debe ser **par** en Simpson

---

## Referencia rápida — Maxima esencial

### Setup obligatorio

```maxima
kill(all)$
fpprec: 400$
```

### Alta precisión

```maxima
bfloat(expr)           /* evaluar con fpprec dígitos */
10^(-155)              /* tolerancia para 150 dígitos — entero exacto, no bfloat() */
bfloat(resultado)      /* ver en decimal un resultado que sale como fracción */
```

### Definir funciones

```maxima
define(f(x), x^2 - bfloat(3))$
define(dfu(x), diff(fu(x), x, 1))$    /* derivada automática */
```

### Listas y matrices

```maxima
makelist(expr, i, a, b)               /* crear lista */
zeromatrix(n, n)                      /* matriz de ceros */
apply('matrix, makelist(...))         /* construir matriz desde listas */
A[i][j]: valor$                       /* asignar elemento */
```

### Operaciones matriciales

```maxima
invert(A)                             /* inversa */
A . B                                 /* producto matricial (PUNTO, no asterisco) */
addcol(A, b)                          /* añadir columna: [A|b] */
echelon(A)                            /* forma escalonada */
transpose(A)                          /* transpuesta */
```

### Álgebra y cálculo

```maxima
expand(expr)                          /* expandir (imprescindible en Lagrange) */
ratsimp(expr)                         /* simplificar fracción */
float(expr)                           /* convertir a decimal */
bfloat(expr)                          /* decimal con alta precisión */
subst(x=val, expr)                    /* sustituir valor */
diff(f(x), x, n)                      /* derivada n-ésima */
integrate(f(x), x, a, b)             /* integral simbólica */
quad_qags(f(x), x, a, b)[1]          /* integral numérica */
```

### Control de flujo

```maxima
block([vars], ..., retorno)           /* función con variables locales */
while condicion do (...)              /* bucle while */
for i:1 thru n do (...)              /* bucle for */
for i:n thru 1 step -1 do (...)      /* bucle for inverso */
if cond then ... else ...$            /* condicional */
oddp(n), evenp(n)                     /* paridad */
```

---

## Errores que cuestan puntos

| Error | Síntoma | Corrección |
|-------|---------|-----------|
| `A * B` para matrices | Resultado incorrecto silencioso | Usar `A . B` |
| `block()` en bisec **recursiva** | Stack overflow / crash | Puro `if-then-else`, sin `block` |
| `bfloat(1), bfloat(2)` como args de bisec | Precisión baja o lentitud | Usar enteros exactos: `1, 2` |
| `fpprec: 160` | Podría fallar con margen justo | Usar `fpprec: 400` |
| Resultado de bisec en fracción | Confusión | Normal — usar `bfloat(resultado)` para ver decimal |
| `return()` fuera de `block` | No hace nada | Solo dentro de `block([...], ...)` |
| Índice 0 en lista | Error "índice fuera de rango" | Maxima usa **base 1**: `L[1]` es el primero |
| `X1[i]` en vez de `X1[i][1]` | Error de dimensión | Resultado de `invert(A).b` es **matriz columna** |
| `n` impar en Simpson | Resultado erróneo | Simpson compuesto requiere `n` **par** |
| Olvidar `[1]` en `quad_qags` | Devuelve lista, no número | `quad_qags(...)[1]` |
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
| **20** | Trapecio/Simpson compuesto + `quad_qags` | 5 | ⭐ Fácil |
| 21 | Simpson compuesto, cota de error | 5 | Fácil |
| 22 | Área por Montecarlo (1 millón de simulaciones) | 5 | Media |
| 23 | Máximo/mínimo numérico | 5 | Media |

> **Resumen de elección**: Ej 2 (página 1) + Ej 20 (página 5) son los más directos. Alternativa igual de fácil: Ej 3 (página 1) + Ej 21 (página 5).

---

*Actualizado: 2026-04-28 · Fuente: final2026.pdf (23 ejercicios) + Sesiones PL 1–8 (wxmx analizados)*
