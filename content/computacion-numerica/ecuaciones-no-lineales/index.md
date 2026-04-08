---
title: Ecuaciones No Lineales — Computación Numérica
description: "Métodos para encontrar raíces de f(x)=0: bisección, punto fijo, Newton-Raphson, secante y métodos para polinomios."
date: 2026-04-08
tags: [computacion-numerica, ecuaciones-no-lineales, raices, universidad]
---

# Ecuaciones No Lineales

Encontrar $r$ tal que $f(r) = 0$ cuando no existe fórmula analítica.

| Método | Orden | Garantía | Necesita |
|--------|-------|----------|---------|
| [[Biseccion-y-Regula-Falsi\|Bisección]] | 1 | Siempre | Intervalo con cambio de signo |
| [[Biseccion-y-Regula-Falsi\|Regula Falsi]] | 1 | Siempre | Intervalo con cambio de signo |
| [[Punto-Fijo]] | 1 | Si $\|g'(r)\| < 1$ | Una función $g$ con punto fijo |
| [[Newton-Raphson]] | **2** | Local | $f'$, estimación inicial buena |
| [[Newton-Raphson\|Secante]] | 1.618 | Local | Solo $f$, dos puntos iniciales |
| [[Ecuaciones-Algebraicas\|Horner + Deflación]] | — | — | Solo para polinomios |
| [[Ecuaciones-Algebraicas\|Müller]] | ~1.84 | Local | Puede hallar raíces complejas |

← [[../index|Computación Numérica]]
