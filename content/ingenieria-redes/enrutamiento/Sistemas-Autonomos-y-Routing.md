---
title: Sistemas Autónomos y Protocolos de Routing
description: "AS, IGP vs EGP, vector-distancia vs estado de enlace. RIP, OSPF, EIGRP, BGP. Convergencia y métricas."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-interconexion-redes.md]
tags: [routing, as, ospf, rip, bgp, igp, egp, ingenieria-redes, universidad]
---

# Sistemas Autónomos y Protocolos de Routing

## Sistema Autónomo (AS)

Conjunto de redes y routers bajo una **única administración** con un plan de routing coherente hacia el exterior. Identificado por un **ASN** (Autonomous System Number).

- Los routers dentro de un AS usan un **protocolo de routing interior (IGP)**.
- Los AS se comunican entre sí mediante un **protocolo de routing exterior (EGP)**.

---

## Routing estático vs dinámico

| | **Estático** | **Dinámico** |
|--|-------------|-------------|
| Configuración | Manual por el administrador | Automática mediante protocolo |
| Adaptación a cambios | No (hay que reconfigurar) | Sí (converge automáticamente) |
| Uso | Redes pequeñas, rutas por defecto | Redes medianas/grandes |
| Comando IOS | `ip route red máscara nexthop` | `router ospf 1` / `router rip` |

**Ruta por defecto (default route):**
```
Router(config)# ip route 0.0.0.0 0.0.0.0 192.168.1.1
```
Envía todo el tráfico sin ruta específica hacia 192.168.1.1.

---

## Clasificación de protocolos de routing dinámico

<svg viewBox="0 0 520 220" width="520" height="220" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <rect x="10" y="10" width="500" height="40" rx="5" fill="#4a7fa5" opacity="0.15"/>
  <text x="260" y="35" font-size="13" fill="currentColor" text-anchor="middle" font-weight="bold">Protocolos de Routing</text>
  <rect x="20" y="70" width="225" height="40" rx="5" fill="#4a7fa5" opacity="0.25"/>
  <text x="132" y="95" font-size="12" fill="currentColor" text-anchor="middle">IGP (interior al AS)</text>
  <rect x="275" y="70" width="225" height="40" rx="5" fill="#c0582a" opacity="0.25"/>
  <text x="387" y="95" font-size="12" fill="currentColor" text-anchor="middle">EGP (entre AS)</text>
  <rect x="20" y="135" width="105" height="55" rx="4" fill="#4a7fa5" opacity="0.2"/>
  <text x="72" y="158" font-size="11" fill="currentColor" text-anchor="middle" font-weight="bold">Vector-distancia</text>
  <text x="72" y="174" font-size="10" fill="currentColor" text-anchor="middle">RIPv2, RIPng</text>
  <text x="72" y="186" font-size="10" fill="currentColor" text-anchor="middle">EIGRP</text>
  <rect x="140" y="135" width="105" height="55" rx="4" fill="#4a7fa5" opacity="0.2"/>
  <text x="192" y="158" font-size="11" fill="currentColor" text-anchor="middle" font-weight="bold">Estado de enlace</text>
  <text x="192" y="174" font-size="10" fill="currentColor" text-anchor="middle">OSPFv2/v3</text>
  <text x="192" y="186" font-size="10" fill="currentColor" text-anchor="middle">IS-IS</text>
  <rect x="290" y="135" width="195" height="55" rx="4" fill="#c0582a" opacity="0.2"/>
  <text x="387" y="158" font-size="11" fill="currentColor" text-anchor="middle" font-weight="bold">Vector de ruta (path vector)</text>
  <text x="387" y="174" font-size="10" fill="currentColor" text-anchor="middle">BGP-4 (IPv4)</text>
  <text x="387" y="186" font-size="10" fill="currentColor" text-anchor="middle">BGP-MP (multiprotocolo)</text>
  <line x1="132" y1="110" x2="85" y2="135" stroke="currentColor" stroke-width="1"/>
  <line x1="132" y1="110" x2="180" y2="135" stroke="currentColor" stroke-width="1"/>
  <line x1="387" y1="110" x2="387" y2="135" stroke="currentColor" stroke-width="1"/>
  <line x1="260" y1="50" x2="132" y2="70" stroke="currentColor" stroke-width="1"/>
  <line x1="260" y1="50" x2="387" y2="70" stroke="currentColor" stroke-width="1"/>
</svg>

---

## Vector-distancia vs Estado de enlace

### Vector-distancia (Distance Vector)

- Cada router **solo conoce sus vecinos directos**, no la topología completa.
- Intercambia vectores de distancia (coste estimado a cada destino) con sus vecinos.
- Algoritmo de **Bellman-Ford**.
- Actualizaciones **periódicas** (cada 30 s en RIP).
- Convergencia **lenta** — la información se propaga "salto a salto".

### Estado de enlace (Link State)

- Cada router **conoce toda la topología** de la red.
- Intercambia LSAs (Link State Advertisements) con todos los routers del área.
- Algoritmo de **Dijkstra (SPF)** para calcular el árbol de menor coste.
- Actualizaciones **por evento** (solo cuando hay cambios).
- Convergencia **rápida**.

---

## RIP (Routing Information Protocol)

Protocolo de **vector-distancia** clásico.

| Parámetro | RIPv1 | RIPv2 |
|-----------|-------|-------|
| Métrica | # saltos (máx 15) | # saltos (máx 15) |
| VLSM | No | Sí |
| Autenticación | No | Sí (MD5) |
| Multicast | No (broadcast) | Sí (224.0.0.9) |
| Classless | No | Sí |

- Distancia máxima: 15 saltos. 16 = infinito (inalcanzable).
- Actualización: cada 30 s. Ruta inválida si no se recibe en 180 s.

**Configuración IOS:**
```
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network 192.168.1.0
Router(config-router)# no auto-summary
```

---

## Distancia Administrativa (AD)

Cuando hay varias fuentes de routing, el router elige según la **distancia administrativa** (menor = más confiable):

| Protocolo | AD |
|-----------|-----|
| Conectado directamente | 0 |
| Estático | 1 |
| OSPF | **110** |
| RIP | **120** |
| EIGRP (externo) | 170 |
| Desconocido | 255 |

---

## Related Topics

- [[OSPF]] — protocolo de estado de enlace más usado
- [[BGP]] — protocolo exterior entre AS
- [[VLANs-y-Conmutacion]] — switching y routing entre VLANs
- [[Guia-Packet-Tracer]] — comandos de configuración y verificación
