---
title: MANETs — Mobile Ad-hoc Networks
description: "MANETs: redes ad-hoc móviles sin infraestructura. Características, desafíos de routing, protocolos AODV y OLSR."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-manets.md]
tags: [manet, adhoc, movil, routing, ingenieria-redes, universidad]
---

# MANETs — Mobile Ad-hoc Networks

## Qué es una MANET

Una **Mobile Ad-hoc Network** es una red inalámbrica sin infraestructura fija:

- Los nodos se comunican directamente entre sí (peer-to-peer).
- Cada nodo actúa también como **router** para reenviar tráfico de otros nodos.
- La topología cambia continuamente porque los nodos se mueven.
- No hay punto central de control ni acceso a Internet obligatorio.

**Ejemplos de uso:** operaciones militares, rescate en desastres, redes vehiculares (VANETs), redes de sensores.

---

## Desafíos

| Desafío | Descripción |
|---------|-------------|
| **Movilidad** | Los nodos se mueven → topología cambiante → las rutas caducan |
| **Recursos limitados** | Batería, ancho de banda, CPU limitados |
| **Medio compartido** | Interferencias, colisiones, problema del nodo oculto |
| **Seguridad** | Sin infraestructura que centralice autenticación |
| **Escalabilidad** | Difícil de escalar a muchos nodos |

---

## Protocolos de routing

### Reactivos (on-demand)

Solo calculan rutas cuando hay tráfico. Bajo overhead cuando el tráfico es esporádico.

**AODV (Ad-hoc On-demand Distance Vector):**
1. Origen envía **RREQ** (Route Request) en broadcast.
2. Cuando llega al destino → responde con **RREP** (Route Reply) unicast de vuelta.
3. Los nodos intermedios actualizan sus tablas de routing.
4. Si la ruta falla → **RERR** (Route Error) notifica al origen.

### Proactivos (table-driven)

Mantienen tablas de routing actualizadas continuamente. Rutas siempre disponibles, pero mayor overhead.

**OLSR (Optimized Link State Routing):**
- Usa **MPRs (Multipoint Relays)**: nodos seleccionados para reenviar mensajes de control, reduciendo el flooding.
- Cada nodo publica su topología local periódicamente.

### Híbridos

**ZRP (Zone Routing Protocol):** proactivo dentro de una zona, reactivo fuera de ella.

---

## VANETs — Vehicular Ad-hoc Networks

Caso especial de MANET para vehículos:
- Alta velocidad de movimiento → topología cambia muy rápido.
- Estándar **IEEE 802.11p** (DSRC) para comunicaciones vehiculares.
- Aplicaciones: seguridad vial, información de tráfico, infotainment.

---

## Related Topics

- [[WLAN]] — 802.11 como base de muchas MANETs
- [[acceso/Acceso-Banda-Ancha]] — acceso móvil 4G/5G como alternativa con infraestructura
