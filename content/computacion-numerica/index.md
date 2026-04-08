---
title: Computación Numérica
description: "Métodos numéricos para resolver problemas matemáticos que no tienen solución analítica exacta: errores, ecuaciones, sistemas, interpolación e integración."
date: 2026-04-08
tags: [computacion-numerica, metodos-numericos, universidad]
---

# Computación Numérica

Asignatura de métodos numéricos. Prof. Alejandro Buendía — Universidad de Oviedo, curso 2025-2026.

> [!tip] Idea fundamental
> Muchos problemas matemáticos reales **no tienen solución analítica** (no se puede "despejar" la incógnita). Los métodos numéricos construyen sucesiones de aproximaciones que convergen a la solución. El precio a pagar: siempre hay **error**, y saber controlarlo es la clave de la asignatura.

---

## Contenido

### [[fundamentos/index|Fundamentos: Aritmética Finita y Error]]
Cómo representan los ordenadores los números reales y qué tipos de error aparecen inevitablemente.

- [[Aritmetica-Finita-y-Error]] — Error absoluto, relativo, cifras significativas, IEEE-754, cancelación catastrófica

### [[ecuaciones-no-lineales/index|Ecuaciones No Lineales]]
Encontrar $r$ tal que $f(r) = 0$ cuando no hay fórmula cerrada.

- [[Biseccion-y-Regula-Falsi]] — Métodos de intervalo: seguros pero lentos
- [[Punto-Fijo]] — Convertir $f(x) = 0$ en $g(x) = x$ e iterar
- [[Newton-Raphson]] — El método más potente: convergencia cuadrática
- [[Ecuaciones-Algebraicas]] — Polinomios: Horner, deflación, Müller

### [[sistemas-lineales/index|Sistemas Lineales y No Lineales]]
Resolver $Ax = b$ de forma eficiente y estable.

- [[Metodos-Directos-Sistemas]] — Gauss, LU, Cholesky, QR, número de condición
- [[Metodos-Iterativos-Sistemas]] — Jacobi, Gauss-Seidel: para sistemas grandes
- [[Sistemas-No-Lineales]] — Punto fijo y Newton vectorial

### [[interpolacion/index|Interpolación y Ajuste de Datos]]
Aproximar una función a partir de valores conocidos en puntos discretos.

- [[Interpolacion-Polinomial]] — Lagrange, Newton con diferencias divididas, error, Chebyshev, splines
- [[Ajuste-de-Datos]] — Mínimos cuadrados, ajuste lineal y no lineal

### [[integracion/index|Integración Numérica]]
Calcular $\int_a^b f(x)\,dx$ cuando no existe primitiva cerrada.

- [[Integracion-Numerica]] — Punto medio, trapecio, Simpson, fórmulas compuestas, cuadratura Gaussiana

---

## Fuentes

- [[fuentes/index|Fuentes]] — 5 temas PDF + 5 hojas de problemas

---

## Personas

- [[Alejandro-Buendia]] — Profesor de la asignatura, Dpto. Matemáticas, Universidad de Oviedo
