---
title: Guía Examen PL Global — Maxima/wxMaxima
type: output
output_type: study-guide
date: 2026-04-24
subject: Computación Numérica
based_on: [Sesion-1-2026.pdf, Sesion-2-2026.pdf, Sesion-4-2026.pdf, Sesion-5-2026.pdf, Sesion-6-2026.pdf, Sesion-7-2026.pdf, Sesion-8-2026.pdf]
tags: [computacion-numerica, maxima, examen, practica, programacion]
---

# Guía Examen PL Global — Maxima/wxMaxima

> **Objetivo**: Aprobar el examen global de prácticas (nota ≥ 4). Ejercicios garantizados confirmados: **Ejercicio 1** (bisec), **Ejercicio 8** (matriz M(n) + sistemas), **Ejercicio 15** (Lagrange).

---

## Sintaxis esencial de Maxima

### Aritmética y precisión

```maxima
/* Precisión estándar (float) */
float(expr)           /* convierte a decimal ~15 dígitos */
sqrt(2), %pi, %e      /* constantes simbólicas */

/* Alta precisión (bfloat) */
fpprec: 50            /* fijar N dígitos decimales */
bfloat(expr)          /* evaluar con N dígitos */
bfloat(sqrt(3))       /* √3 con 50 dígitos */
```

### Definir funciones

```maxima
/* Forma 1: define */
define(f(x), x^2 - 3)

/* Forma 2: := */
f(x) := x^2 - 3

/* Forma 3: lambda */
f: lambda([x], x^2 - 3)

/* Derivadas */
diff(f(x), x)         /* primera derivada */
diff(f(x), x, 2)      /* segunda derivada */

/* Sustitución */
subst(x=2, f(x))      /* f(2) simbólico */
f(2)                   /* f(2) numérico si f está definida */
```

### Listas y bucles

```maxima
/* Crear listas */
makelist(i^2, i, 1, 5)          /* [1,4,9,16,25] */
makelist(expr, var, lista)
append(L1, L2)                   /* concatenar */
length(L)                        /* tamaño */
L[i]                             /* acceder elemento i (base 1) */

/* Bucle for */
for i:1 thru n do (
    /* cuerpo */
)

/* Bucle while */
while condicion do (
    /* cuerpo */
)

/* Acumular resultado */
resultado: []
for i:1 thru 5 do
    resultado: append(resultado, [i^2])
```

### Block y return

```maxima
/* block define variables locales y retorna el último valor */
block([x, y, z],
    x: 1,
    y: 2,
    z: x + y,
    z              /* retorna z */
)

/* return() sale del block antes */
block([i],
    for i:1 thru 100 do (
        if i^2 > 50 then return(i)
    )
)
```

### Álgebra simbólica

```maxima
expand(expr)          /* expandir */
factor(expr)          /* factorizar */
ratsimp(expr)         /* simplificar fracción racional */
simplify(expr)        /* simplificación general */
subst(a=val, expr)    /* sustituir */
solve(eq, x)          /* resolver simbólicamente */
```

---

## Ejercicio 1 garantizado — `bisec(fu, a, b, tol)`

### Enunciado tipo
Implementar la función `bisec(fu, a, b, tol)` que aplique el método de bisección para encontrar una raíz de `fu` en `[a,b]` con tolerancia `tol`. Aplicarla para calcular `√3` con 150 dígitos correctos.

### Versión iterativa (más segura en el examen)

```maxima
bisec(fu, a, b, tol) := block(
    [c, fa, fb, fc],
    fa: fu(a),
    fb: fu(b),
    /* Verificar condición de Bolzano */
    if fa * fb > 0 then (
        print("Error: no hay cambio de signo en [a,b]"),
        return(false)
    ),
    /* Iterar hasta convergencia */
    while (b - a) / 2 > tol do (
        c: (a + b) / 2,
        fc: fu(c),
        if fc = 0 then return(c),   /* raíz exacta */
        if fa * fc < 0 then (
            b: c,
            fb: fc
        ) else (
            a: c,
            fa: fc
        )
    ),
    (a + b) / 2   /* retorna punto medio final */
)$
```

### Versión recursiva (alternativa válida)

```maxima
bisec_rec(fu, a, b, tol) := block(
    [c, fa, fc],
    c: (a + b) / 2,
    fc: fu(c),
    if (b - a) / 2 < tol then return(c),
    if fc = 0 then return(c),
    fa: fu(a),
    if fa * fc < 0 then
        bisec_rec(fu, c, b, tol)
    else
        bisec_rec(fu, a, c, tol)
)$
```

### Aplicación: calcular √3 con 150 dígitos

```maxima
/* Fijar precisión ANTES de definir y llamar */
fpprec: 160   /* un poco más que 150 para seguridad */

/* Definir función con bfloat para alta precisión */
define(g(x), x^2 - bfloat(3))

/* Llamar bisec con tolerancia acorde */
raiz3: bisec(g, bfloat(1), bfloat(2), bfloat(10)^(-155))$
print(raiz3)$

/* Verificar */
print("Error:", abs(raiz3^2 - 3))$
```

> **Truco clave**: usar `bfloat(1)`, `bfloat(2)` como extremos (no `1`, `2`), y `bfloat(10)^(-155)` como tolerancia. Si los argumentos son `float`, la precisión no sube.

### Número de iteraciones necesario

Para obtener `n` dígitos correctos con bisección en `[a,b]`:
$$N \geq \frac{n \cdot \ln(10) + \ln(b-a)}{\ln(2)}$$

Para `[1,2]`, 150 dígitos: $N \geq \lceil 150 \cdot 3.3219 \rceil = 499$ iteraciones.

---

## Ejercicio 8 garantizado — Matriz M(n) y sistemas lineales

### Enunciado tipo
Definir la matriz `M(n)` (11 en la diagonal, 1 si `|i-j| < 3`, 0 en caso contrario) y el vector `b(n) = [1,1,...,1]`. Resolver `M(7)·X = b(7)` por:
1. `invert(M(7)) · b(7)`
2. `echelon(addcol(M(7), b(7)))` + sustitución regresiva

### Definición de M(n)

```maxima
M(n) := block(
    [A],
    A: zeromatrix(n, n),
    for i:1 thru n do
        for j:1 thru n do
            if i = j then A[i][j]: 11
            else if abs(i - j) < 3 then A[i][j]: 1,
    A
)$

b(n) := transpose(matrix(makelist(1, i, 1, n)))$
/* O bien: */
b(n) := genmatrix(lambda([i,j], 1), n, 1)$
```

> **Nota**: `A[i][j]` indexa en base 1. `zeromatrix(n,n)` crea matriz de ceros.

### Método 1: Inversa

```maxima
X1: invert(M(7)) . b(7)$
print("Solución por inversa:", X1)$

/* Suma de componentes */
suma: sum(X1[i][1], i, 1, 7)$
print("Suma x1+...+x7 =", suma)$
```

### Método 2: Echelon + sustitución regresiva

```maxima
/* Crear matriz aumentada [M|b] */
Aug: addcol(M(7), b(7))$

/* Reducción a forma escalonada */
E: echelon(Aug)$
print("Forma escalonada:", E)$

/* Sustitución regresiva manual */
back_sub(E, n) := block(
    [x, i, j, s],
    x: makelist(0, k, 1, n),
    for i:n thru 1 step -1 do (
        s: E[i][n+1],
        for j:i+1 thru n do
            s: s - E[i][j] * x[j],
        x[i]: ratsimp(s / E[i][i])
    ),
    x
)$

X2: back_sub(E, 7)$
print("Solución:", X2)$
suma2: sum(X2[i], i, 1, 7)$
print("Suma:", suma2)$
```

### Operaciones matriciales esenciales

```maxima
/* Multiplicación: usar . (punto) NO * */
A . B             /* producto matricial */
A . v             /* matriz por vector */

/* Inversa y transpuesta */
invert(A)
transpose(A)

/* Determinante y traza */
determinant(A)
mat_trace(A)

/* Potencia matricial */
A^^n              /* A^n (n entera) */
A^^(-1)           /* inversa */

/* Resolver sistema simbólico */
linsolve([eq1, eq2, eq3], [x, y, z])

/* Echelon: devuelve forma escalonada de filas */
echelon(A)
triangularize(A)  /* similar, más eficiente */
```

---

## Ejercicio 15 garantizado — Interpolación de Lagrange

### Enunciado tipo
Implementar `Lagrange(fu, nodos)` que calcule el polinomio de interpolación de Lagrange para la función `fu` en los nodos dados. Testear con un polinomio de grado 6 en 7 nodos.

### Implementación

```maxima
Lagrange(fu, nodos) := expand(
    sum(
        fu(nodos[i]) *
        product(
            (x - nodos[j]) / (nodos[i] - nodos[j]),
            j, 1, length(nodos),
            j # i     /* j distinto de i */
        ),
        i, 1, length(nodos)
    )
)$
```

### Test con polinomio de grado 6

```maxima
/* Función de prueba: polinomio grado 6 */
define(p(x), x^6 - 2*x^5 + 3*x^4 - x^3 + x - 1)

/* 7 nodos equiespaciados en [-1, 1] */
nodos7: makelist(-1 + 2*i/6, i, 0, 6)$
/* = [-1, -2/3, -1/3, 0, 1/3, 2/3, 1] */

/* Calcular polinomio de Lagrange */
P: Lagrange(p, nodos7)$
print("Polinomio interpolante:", expand(P))$

/* Verificar: debe coincidir con p(x) exactamente */
error: expand(P - p(x))$
print("Error (debe ser 0):", error)$
```

### Variante con nodos Chebyshev (mejor aproximación)

```maxima
/* Nodos Chebyshev en [a,b] */
chebyshev_nodos(n, a, b) :=
    makelist(
        (a+b)/2 + (b-a)/2 * cos((2*k-1)*%pi/(2*n)),
        k, 1, n
    )$

nodos_ch: chebyshev_nodos(7, -1, 1)$
P_ch: Lagrange(p, nodos_ch)$
```

### Evaluar el polinomio interpolante

```maxima
/* Evaluar P en un punto */
subst(x=0.5, P)$
float(%)$

/* Comparar con función original */
error_punto: abs(subst(x=0.5, P) - p(0.5))$
```

---

## Patrones de sesiones PL (sesiones 1–8)

### Sesión 1 — bfloat, series de Taylor

```maxima
/* Calcular función con alta precisión */
fpprec: 50$
bfloat(%e)           /* e con 50 dígitos */
bfloat(%pi)          /* π con 50 dígitos */

/* Serie de Taylor de e^x en x=0, orden n */
taylor_exp(x, n) := sum(x^k / k!, k, 0, n)$

/* Función de Taylor genérica */
DIF(fu, x0, n) := block(
    [serie, k],
    serie: 0,
    for k:0 thru n do
        serie: serie + subst(x=x0, diff(fu(x),x,k)) / k! * (x-x0)^k,
    expand(serie)
)$
```

### Sesión 2 — Factorial, Fibonacci, Collatz

```maxima
/* Factorial recursivo */
fact_rec(n) := if n=0 then 1 else n * fact_rec(n-1)$

/* Factorial iterativo */
fact_it(n) := block([r:1, i], for i:1 thru n do r:r*i, r)$

/* Factorial con product */
fact_prod(n) := product(i, i, 1, n)$

/* Fibonacci iterativo */
fib(n) := block(
    [a:0, b:1, c, i],
    if n=0 then return(0),
    if n=1 then return(1),
    for i:2 thru n do (c:a+b, a:b, b:c),
    b
)$

/* Secuencia de Collatz */
collatz(n) := block(
    [seq:[n], k:n],
    while k#1 do (
        if evenp(k) then k:k/2 else k:3*k+1,
        seq: append(seq, [k])
    ),
    seq
)$
```

### Sesión 4 — Newton-Raphson y Punto Fijo

```maxima
/* Newton-Raphson */
newton(fu, x0, tol) := block(
    [x:x0, xnew, fval, dfval, i:0],
    loop: (
        fval: fu(x),
        dfval: subst(t=x, diff(fu(t), t)),
        if abs(dfval) < 1e-15 then (print("Derivada cero"), return(false)),
        xnew: x - fval/dfval,
        i: i+1,
        if abs(xnew - x) < tol then return(xnew),
        x: xnew,
        go(loop)
    )
)$

/* Punto fijo */
punto_fijo(g, x0, tol) := block(
    [x:x0, xnew],
    while true do (
        xnew: g(x),
        if abs(xnew - x) < tol then return(xnew),
        x: xnew
    )
)$

/* Método de la secante */
secante(fu, x0, x1, tol) := block(
    [a:x0, b:x1, c],
    while abs(b-a) > tol do (
        c: b - fu(b)*(b-a)/(fu(b)-fu(a)),
        a: b,
        b: c
    ),
    b
)$
```

### Sesión 5 — Sistemas lineales

```maxima
/* Resolver con linsolve */
linsolve([2*x + y = 5, x - y = 1], [x, y])$

/* Jacobi */
jacobi(A, b, x0, tol) := block(
    [n:length(b), x:x0, xnew, i, j, s],
    xnew: makelist(0, i, 1, n),
    while true do (
        for i:1 thru n do (
            s: b[i],
            for j:1 thru n do
                if j#i then s: s - A[i][j]*x[j],
            xnew[i]: s/A[i][i]
        ),
        if lmax(abs(xnew - x)) < tol then return(xnew),
        x: copylist(xnew)
    )
)$

/* Cholesky (verificar que A sea definida positiva) */
/* Maxima tiene: cholesky(A) */
/* Pero en examen se puede pedir implementarlo */
```

### Sesión 6 — Newton-Raphson multidimensional

```maxima
/* Newton-Raphson para sistemas F(x)=0 */
newton_nd(F_vec, vars, x0, tol) := block(
    [x:x0, J, Fval, dx, i],
    for i:1 thru 100 do (
        Fval: float(subst(map("=", vars, x), F_vec)),
        J: float(subst(map("=", vars, x), jacobian(F_vec, vars))),
        dx: float(- invert(J) . matrix(Fval)),
        x: x + transpose(dx)[1],
        if lmax(abs(Fval)) < tol then return(x)
    ),
    x
)$

/* Ejemplo: resolver x^2+y^2=4, x-y=0 */
F: [x^2 + y^2 - 4, x - y]$
jacobian(F, [x,y])$  /* [[2x, 2y], [1, -1]] */
```

### Sesión 7 — Interpolación y matrices

```maxima
/* Lagrange (ya visto en Ejercicio 15) */

/* Evaluar polinomio en varios puntos */
vals: float(makelist(subst(x=t, P), t, nodos))$

/* Funciones de matriz via autovalores */
/* Para f(A) donde f es una función escalar */
matfuncion(A, fu) := block(
    [evals, evecs, D, P, Pinv],
    /* Diagonalizar A = P D P^-1 */
    /* Aplicar fu a cada autovalor */
    /* Reconstruir: P diag(fu(λ1),...) P^-1 */
    eigenvalues(A)   /* [[lambdas], [multiplicidades]] */
)$
```

### Sesión 8 — Integración numérica

```maxima
/* Integración numérica con Maxima */
quad_qags(sin(x), x, 0, %pi)    /* [valor, error_abs, ...] */
/* Devuelve lista: usar [1] para el valor */
resultado: quad_qags(sin(x), x, 0, %pi)[1]$

/* Regla del rectángulo */
rectangulo(fu, a, b, n) := block(
    [h:(b-a)/n, s:0, i],
    for i:0 thru n-1 do
        s: s + fu(a + (i+0.5)*h),
    h*s
)$

/* Regla del trapecio compuesta */
trapecio(fu, a, b, n) := block(
    [h:(b-a)/n, s:fu(a)+fu(b), i],
    for i:1 thru n-1 do
        s: s + 2*fu(a + i*h),
    h*s/2
)$

/* Simpson compuesta (n debe ser par) */
simpson(fu, a, b, n) := block(
    [h:(b-a)/n, s:fu(a)+fu(b), i],
    for i:1 thru n-1 do (
        if oddp(i) then s: s + 4*fu(a + i*h)
                   else s: s + 2*fu(a + i*h)
    ),
    h*s/3
)$

/* Monte Carlo */
monte_carlo(fu, a, b, N) := block(
    [puntos: makelist(a + random(float(b-a)), i, 1, N)],
    (b-a) * sum(fu(puntos[i]), i, 1, N) / N
)$
```

---

## Tabla de errores frecuentes en el examen

| Error | Síntoma | Solución |
|-------|---------|----------|
| Usar `*` para matrices | Error de tipo | Usar `.` (punto) para producto matricial |
| `b-a` en bisec con bfloat | Precisión perdida si `a,b` son enteros | `bfloat(b) - bfloat(a)` o pasar `bfloat(1)` desde el principio |
| `return()` fuera de `block` | No funciona | `return()` solo funciona dentro de `block()` |
| Índice 0 en listas | Error de índice | Maxima usa índice **base 1** |
| `j # i` en product | Puede fallar si no está en modo algebraico | Usar `if j # i then ...` dentro de un `sum` con `block` |
| `fpprec` no afecta | Se olvidó `bfloat()` en el cálculo | Toda operación de alta precisión necesita `bfloat()` explícito |
| División por cero en NR | `f'(x)=0` en alguna iteración | Añadir guarda `if abs(dfval) < eps then return(false)` |
| `echelon` devuelve fracciones | Normal | Usar `ratsimp` o `float` según el contexto |
| `linsolve` da lista vacía | Sistema inconsistente o dependiente | Verificar con `rank(A)` vs `rank(addcol(A,b))` |

---

## Receta de supervivencia para el examen

### Al inicio del examen

```maxima
/* Siempre ejecutar primero: */
kill(all)$           /* limpiar estado previo */
fpprec: 160$         /* alta precisión por si acaso */
```

### Estructura de cualquier función

```maxima
mi_funcion(parametros) := block(
    [var_local1, var_local2],    /* declarar variables locales */
    /* ... cuerpo ... */
    resultado                    /* última expresión = valor de retorno */
)$                               /* $ suprime output; : muestra */
```

### Verificar siempre el resultado

```maxima
/* Después de implementar, SIEMPRE testear: */
/* 1. Con un caso conocido (ej: raíz exacta) */
/* 2. Imprimir intermediate values si algo falla */
print("Valor en x=2:", f(2))$
```

### Comandos de diagnóstico

```maxima
/* Ver qué está definido */
values$          /* variables definidas */
functions$       /* funciones definidas */

/* Información de una función */
describe(echelon)$
?? echelon$      /* ayuda */

/* Limpiar una variable */
kill(x)$
remvalue(x)$
```

---

## Referencia rápida de funciones clave

| Función | Uso | Ejemplo |
|---------|-----|---------|
| `bfloat(x)` | Alta precisión | `bfloat(sqrt(2))` |
| `fpprec: n` | Fijar dígitos | `fpprec: 50` |
| `define(f(x), expr)` | Definir función | `define(f(x), x^2-1)` |
| `diff(f(x),x,n)` | Derivada n-ésima | `diff(sin(x),x,2)` |
| `subst(x=a, expr)` | Sustituir | `subst(x=0, f(x))` |
| `expand(expr)` | Expandir | `expand((x+1)^3)` |
| `ratsimp(expr)` | Simplificar | `ratsimp(x^2-1)/(x-1))` |
| `float(expr)` | Convertir a float | `float(%pi)` |
| `makelist(e,i,a,b)` | Crear lista | `makelist(i^2,i,1,5)` |
| `matrix([r1],[r2])` | Crear matriz | `matrix([1,2],[3,4])` |
| `zeromatrix(n,m)` | Matriz de ceros | `zeromatrix(3,3)` |
| `ident(n)` | Matriz identidad | `ident(4)` |
| `invert(A)` | Inversa | `invert(M(7))` |
| `transpose(A)` | Transpuesta | `transpose(v)` |
| `determinant(A)` | Determinante | `determinant(A)` |
| `echelon(A)` | Forma escalonada | `echelon(addcol(A,b))` |
| `addcol(A,B)` | Aumentar matriz | `addcol(M, b)` |
| `linsolve(eqs,vars)` | Resolver sistema | `linsolve([...],[x,y])` |
| `jacobian(F,vars)` | Jacobiano | `jacobian([f,g],[x,y])` |
| `find_root(f,x,a,b)` | Raíz numérica | `find_root(f(x),x,0,1)` |
| `quad_qags(f,x,a,b)` | Integral numérica | `quad_qags(sin(x),x,0,%pi)` |
| `eigenvalues(A)` | Autovalores | `eigenvalues(A)` |
| `eigenvectors(A)` | Autovectores | `eigenvectors(A)` |
| `sum(e,i,a,b)` | Suma simbólica | `sum(i^2,i,1,n)` |
| `product(e,i,a,b)` | Producto | `product(i,i,1,n)` |
| `abs(x)` | Valor absoluto | `abs(x-1)` |
| `max(a,b)`, `lmax(L)` | Máximo | `lmax(abs(L))` |
| `evenp(n)`, `oddp(n)` | Paridad | `if evenp(n) then ...` |
| `copylist(L)` | Copia de lista | `x: copylist(xnew)` |

---

*Generado: 2026-04-24 · Fuentes: Sesiones PL 1–8, imágenes del examen real*
