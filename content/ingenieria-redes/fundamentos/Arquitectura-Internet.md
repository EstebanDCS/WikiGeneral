---
title: Arquitectura de la Red Internet
description: "Internet como red de redes: jerarquía ISP, sistemas autónomos, IXPs, pila TCP/IP y direccionamiento MAC/IP."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-arquitectura-internet.md]
tags: [internet, arquitectura, isp, as, tcp-ip, ingenieria-redes, universidad]
---

# Arquitectura de la Red Internet

## Qué es Internet

Internet es una **red de redes**: múltiples redes físicas heterogéneas interconectadas por routers, que utilizan la familia de protocolos **TCP/IP** como lenguaje común. Cada red individual puede tener sus propias características internas.

> Internet es descentralizado — ninguna entidad lo controla por completo. Está gobernado por un ecosistema de organizaciones.

**Organismos clave:**

| Organización | Función |
|---|---|
| **IANA** (1972) | Asigna direcciones IP y números de AS |
| **IETF** (1986) | Desarrolla estándares y protocolos (RFCs) |
| **ICANN** (1998) | Coordina DNS y nombres de dominio |
| **NRO / RIRs** (2003) | Registros regionales de Internet (RIPE, ARIN, APNIC, LACNIC, AfriNIC) |

---

## Jerarquía de Internet

<svg viewBox="0 0 520 240" width="520" height="240" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <rect x="190" y="15" width="140" height="35" rx="5" fill="#4a7fa5" opacity="0.8"/>
  <text x="260" y="37" font-size="12" fill="white" text-anchor="middle">Tier 1 (backbone)</text>
  <rect x="60" y="85" width="120" height="35" rx="5" fill="#5ba85e" opacity="0.8"/>
  <text x="120" y="107" font-size="12" fill="white" text-anchor="middle">Tier 2 ISP</text>
  <rect x="340" y="85" width="120" height="35" rx="5" fill="#5ba85e" opacity="0.8"/>
  <text x="400" y="107" font-size="12" fill="white" text-anchor="middle">Tier 2 ISP</text>
  <rect x="40" y="165" width="100" height="35" rx="5" fill="#c0582a" opacity="0.7"/>
  <text x="90" y="187" font-size="11" fill="white" text-anchor="middle">Tier 3 (cliente)</text>
  <rect x="160" y="165" width="100" height="35" rx="5" fill="#c0582a" opacity="0.7"/>
  <text x="210" y="187" font-size="11" fill="white" text-anchor="middle">Tier 3 (cliente)</text>
  <rect x="360" y="165" width="100" height="35" rx="5" fill="#c0582a" opacity="0.7"/>
  <text x="410" y="187" font-size="11" fill="white" text-anchor="middle">Tier 3 (cliente)</text>
  <line x1="120" y1="85" x2="210" y2="50" stroke="currentColor" stroke-width="1.5"/>
  <line x1="400" y1="85" x2="310" y2="50" stroke="currentColor" stroke-width="1.5"/>
  <line x1="120" y1="85" x2="400" y2="85" stroke="currentColor" stroke-width="1.5" stroke-dasharray="5,3"/>
  <text x="245" y="80" font-size="10" fill="currentColor" opacity="0.6">peering</text>
  <line x1="90" y1="165" x2="100" y2="120" stroke="currentColor" stroke-width="1.5"/>
  <line x1="210" y1="165" x2="140" y2="120" stroke="currentColor" stroke-width="1.5"/>
  <line x1="410" y1="165" x2="390" y2="120" stroke="currentColor" stroke-width="1.5"/>
  <text x="50" y="220" font-size="10" fill="#4a7fa5">Tier 1: acceso global sin pago (peering)</text>
  <text x="50" y="234" font-size="10" fill="#5ba85e">Tier 2: peering parcial + transit   Tier 3: paga todo el tránsito</text>
</svg>

- **Tier 1**: acceden a toda Internet sin pagar a nadie (peering entre iguales). Ejemplos: Level 3, AT&T, NTT, Deutsche Telekom.
- **Tier 2**: se alían con otras redes, pero aún contratan tráfico a Tier 1.
- **Tier 3**: ISPs locales que contratan todo su tráfico.

**Conceptos clave:**
- **Peering**: intercambio de tráfico entre dos redes sin pago (beneficio mutuo).
- **Transit**: pagar a otra red para que reenvíe tu tráfico.
- **IXP** (Internet Exchange Point): infraestructura física donde ISPs intercambian tráfico.
- **PoP** (Point of Presence): lugar físico donde un ISP tiene equipamiento.
- **Single/Dual/Multi-home**: número de conexiones de un cliente a su ISP.

---

## Sistema Autónomo (AS)

Conjunto de redes y routers bajo una única administración, identificados por un **ASN** (Autonomous System Number) gestionado por IANA → RIRs.

- **IGP** (Interior Gateway Protocol): protocolo de routing dentro del AS. Ejemplos: OSPF, RIP, EIGRP.
- **EGP** (Exterior Gateway Protocol): entre AS distintos. Ejemplo: **BGP**.

---

## Modelo TCP/IP y capas

| Capa | Función | Protocolos/tecnologías | Dirección |
|------|---------|----------------------|-----------|
| **Aplicación** | Servicios de usuario | HTTP, SMTP, FTP, DNS | — |
| **Transporte** | Control extremo a extremo | TCP, UDP | Puerto (16 bits) |
| **Internet (IP)** | Encaminamiento entre redes | IPv4, IPv6 | IP (32/128 bits) |
| **Enlace** | Control de acceso al medio | Ethernet (802.3), WiFi (802.11) | MAC (48 bits) |
| **Física** | Transmisión de bits | Cables, radio, fibra | — |

**Proceso de encapsulación:**
```
APLICACIÓN → datos
TRANSPORTE  → [TCP header | datos]        ← segmento
IP          → [IP header | TCP | datos]   ← datagrama
ENLACE      → [MAC header | IP | ... | FCS] ← trama
```

### Direcciones MAC vs IP

- **MAC (EUI-48):** `00:19:0E:0A:45:63` — identifica la interfaz de red (nivel L2). Asignada por el fabricante. Solo tiene sentido en la red local.
- **IP:** `156.35.14.2` — identifica el nodo en Internet. Única globalmente. Permite el routing entre redes.

> Un switch trabaja con MACs (L2). Un router trabaja con IPs (L3).

---

## Procesado de paquetes en routers y switches

- **Switch (L2):** recibe una trama, consulta su tabla MAC, la reenvía por el puerto correcto. Si no conoce la MAC destino → broadcast.
- **Router (L3):** recibe un datagrama IP, consulta la tabla de routing por la IP destino, reescribe las MACs de origen y destino para el siguiente salto, y reenvía.

---

## Related Topics

- [[Sistemas-Autonomos-y-Routing]] — protocolos de routing entre AS
- [[IPv6]] — la versión 6 de IP y su adopción
- [[Subredes-y-CIDR]] — cómo se divide el espacio de direcciones
- [[OSPF]] — protocolo IGP de estado de enlace
- [[BGP]] — protocolo EGP entre AS
