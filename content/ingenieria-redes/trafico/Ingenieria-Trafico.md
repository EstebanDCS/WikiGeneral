---
title: Ingeniería de Tráfico de Datos
description: "Ingeniería de tráfico: modelos de tráfico, Erlang, DOCSIS y gestión de capacidad en redes de acceso. QoS y priorización."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-ingenieria-trafico-datos.md, ird-redes-cable-docsis.md]
tags: [trafico, qos, erlang, docsis, ingenieria-redes, universidad]
---

# Ingeniería de Tráfico de Datos

## Modelos de tráfico

El tráfico en redes puede modelarse para dimensionar la capacidad necesaria:

**Tráfico de datos:** bursty (a ráfagas), no determinista. Se caracteriza por:
- **Tasa media** (Mbps)
- **Tasa de pico** (Mbps)
- **Tamaño de ráfaga**

**Multiplexación estadística:** al agregar muchos flujos, las ráfagas se promedian → la capacidad necesaria es menor que la suma de los picos individuales. Principio base de las redes de paquetes.

---

## Erlang — Tráfico de voz

El modelo de Erlang es fundamental para dimensionar circuitos de voz:

**Erlang B:** sistema sin cola (bloqueante). Si todos los circuitos están ocupados → la llamada se bloquea.

$$E_B(A, N) = \frac{A^N/N!}{\sum_{k=0}^{N} A^k/k!}$$

Donde:
- $A$ = tráfico ofrecido en Erlangs (intensidad)
- $N$ = número de circuitos
- $E_B$ = probabilidad de bloqueo

**1 Erlang** = un circuito ocupado el 100% del tiempo.

**Erlang C:** sistema con cola (no bloqueante). Las llamadas esperan cuando todos los circuitos están ocupados.

---

## DOCSIS — Gestión de capacidad

En redes HFC (cable), el canal upstream es compartido entre todos los módems del segmento. La gestión de acceso es clave:

**MAP messages (Upstream Channel Descriptor):**
- El CMTS envía periódicamente mensajes MAP que asignan **minislots** a cada módem.
- Cada módem transmite **solo en su slot asignado**.
- Si un módem no tiene datos → el slot se puede asignar a otro (asignación dinámica).

**Tipos de asignación de slots:**
- **Unicast grants**: slots asignados a un módem específico.
- **Contention slots**: slots abiertos — cualquier módem puede transmitir (para peticiones nuevas).

**Problema de los contention slots:** si dos módems transmiten a la vez → colisión. Resuelto con **backoff exponencial** (como Ethernet).

---

## QoS — Quality of Service

**Por qué QoS:** en redes de paquetes compartidas, el tráfico compite por ancho de banda. Sin QoS, todos los paquetes tienen la misma prioridad.

**Clases de tráfico:**

| Clase | Requisitos | Ejemplos |
|-------|-----------|---------|
| **Tiempo real** | Baja latencia, jitter mínimo | VoIP, videoconferencia |
| **Interactivo** | Latencia media | Gaming, SSH |
| **Streaming** | Ancho de banda garantizado | Netflix, YouTube |
| **Best-effort** | Sin garantías | Email, descargas |

**Mecanismos de QoS:**
- **Clasificación y marcado**: DiffServ (DSCP en IP header).
- **Colas de prioridad**: Strict Priority Queue (SPQ), Weighted Fair Queuing (WFQ).
- **Traffic shaping**: suavizar ráfagas.
- **Policing**: descartar exceso sobre un umbral.

---

## Ingeniería de tráfico con MPLS-TE

MPLS permite definir rutas explícitas (LSPs) para desviar tráfico de rutas congestionadas:

- **RSVP-TE**: protocolo de señalización para reservar recursos (ancho de banda) en los LSPs.
- Permite usar links menos cargados aunque no sean el camino más corto según IGP.
- Fundamental en redes de operador (Tier 1/2).

---

## Related Topics

- [[MPLS]] — ingeniería de tráfico con etiquetas
- [[acceso/Acceso-Banda-Ancha]] — capacidad de las redes de acceso
- [[trafico/VoIP]] — tráfico de voz sobre IP
