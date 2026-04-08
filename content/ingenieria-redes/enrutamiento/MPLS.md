---
title: MPLS — Multiprotocol Label Switching
description: "MPLS: conmutación por etiquetas en lugar de routing IP. LER, LSR, LDP, LSP. Ventajas sobre IP puro. Traffic engineering y VPNs."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-mpls.md]
tags: [mpls, switching, etiquetas, trafico, ingenieria-redes, universidad]
---

# MPLS — Multiprotocol Label Switching

## Problema que resuelve

En una red IP grande, cada router debe hacer una búsqueda en la tabla de routing por **longest prefix match** para cada paquete. Esto es costoso y no permite rutas predefinidas ni ingeniería de tráfico.

**MPLS** añade una capa de **etiquetas** entre L2 y L3 (a veces llamada "capa 2.5") para acelerar el reenvío y permitir rutas explícitas.

---

## Conceptos clave

| Término | Significado |
|---------|-------------|
| **Label** | Número de 20 bits que identifica el camino (no el destino) |
| **LSP** (Label Switched Path) | Ruta predefinida de extremo a extremo por etiquetas |
| **LER** (Label Edge Router) | Router de borde que añade/quita etiquetas |
| **LSR** (Label Switch Router) | Router interior que conmuta por etiquetas |
| **FEC** (Forwarding Equivalence Class) | Conjunto de paquetes que siguen el mismo camino |
| **LDP** (Label Distribution Protocol) | Protocolo que distribuye las etiquetas entre routers |

---

## Cabecera MPLS

La etiqueta MPLS se inserta entre la cabecera L2 (Ethernet) y L3 (IP):

```
[Ethernet | MPLS Label (32 bits) | IP | payload]
```

**Campos de la etiqueta (32 bits):**
- **Label** (20 bits): valor de la etiqueta
- **TC/EXP** (3 bits): clase de tráfico (QoS)
- **S** (1 bit): Stack bit — indica si es la última etiqueta (bottom of stack)
- **TTL** (8 bits): Time To Live

Se pueden apilar múltiples etiquetas (**label stacking**).

---

## Funcionamiento

```
Host A → LER (ingreso) → LSR1 → LSR2 → LER (egreso) → Host B
           añade etiqueta  swap   swap    quita etiqueta
```

1. El **LER de ingreso** clasifica el paquete IP en una FEC y añade la etiqueta correspondiente.
2. Los **LSRs** hacen **swap** (cambian la etiqueta por otra) y reenvían sin mirar la IP.
3. El **LER de egreso** quita la etiqueta y reenvía el paquete IP normalmente.

**PHP (Penultimate Hop Popping):** el LSR anterior al LER de egreso quita ya la etiqueta, reduciendo el trabajo del LER.

---

<svg viewBox="0 0 520 150" width="520" height="150" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <!-- Nodes -->
  <rect x="10" y="50" width="60" height="40" rx="4" fill="#888" opacity="0.3"/>
  <text x="40" y="75" font-size="9" fill="currentColor" text-anchor="middle">Host A</text>
  <rect x="90" y="40" width="70" height="50" rx="4" fill="#4a7fa5" opacity="0.5"/>
  <text x="125" y="62" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">LER</text>
  <text x="125" y="76" font-size="8" fill="currentColor" text-anchor="middle">añade etiq.</text>
  <rect x="185" y="45" width="60" height="40" rx="4" fill="#c0582a" opacity="0.4"/>
  <text x="215" y="65" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">LSR</text>
  <text x="215" y="77" font-size="8" fill="currentColor" text-anchor="middle">swap</text>
  <rect x="270" y="45" width="60" height="40" rx="4" fill="#c0582a" opacity="0.4"/>
  <text x="300" y="65" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">LSR</text>
  <text x="300" y="77" font-size="8" fill="currentColor" text-anchor="middle">swap</text>
  <rect x="355" y="40" width="70" height="50" rx="4" fill="#4a7fa5" opacity="0.5"/>
  <text x="390" y="62" font-size="9" fill="currentColor" text-anchor="middle" font-weight="bold">LER</text>
  <text x="390" y="76" font-size="8" fill="currentColor" text-anchor="middle">quita etiq.</text>
  <rect x="450" y="50" width="60" height="40" rx="4" fill="#888" opacity="0.3"/>
  <text x="480" y="75" font-size="9" fill="currentColor" text-anchor="middle">Host B</text>
  <!-- Lines -->
  <line x1="70" y1="70" x2="90" y2="70" stroke="currentColor" stroke-width="1.5"/>
  <line x1="160" y1="65" x2="185" y2="65" stroke="currentColor" stroke-width="2"/>
  <line x1="245" y1="65" x2="270" y2="65" stroke="currentColor" stroke-width="2"/>
  <line x1="330" y1="65" x2="355" y2="65" stroke="currentColor" stroke-width="2"/>
  <line x1="425" y1="65" x2="450" y2="70" stroke="currentColor" stroke-width="1.5"/>
  <!-- Label indicators -->
  <text x="172" y="55" font-size="8" fill="#c0582a">[L=100]</text>
  <text x="257" y="55" font-size="8" fill="#c0582a">[L=200]</text>
  <text x="342" y="55" font-size="8" fill="#c0582a">[L=300]</text>
  <!-- Domain label -->
  <text x="280" y="120" font-size="9" fill="currentColor" text-anchor="middle" opacity="0.6">Núcleo MPLS (LSPs predefinidos)</text>
</svg>

---

## Ventajas de MPLS

1. **Velocidad**: conmutación por etiqueta más rápida que búsqueda IP (aunque con hardware moderno la diferencia disminuye).
2. **Ingeniería de tráfico (MPLS-TE)**: rutas explícitas que evitan cuellos de botella.
3. **VPNs (MPLS L3 VPN)**: múltiples clientes comparten la misma infraestructura con aislamiento total.
4. **QoS**: el campo EXP permite priorizar tráfico.
5. **Independiente del protocolo**: funciona sobre cualquier L2 (Ethernet, ATM, FR).

---

## MPLS VPN

La aplicación más extendida de MPLS en operadores. Permite ofrecer VPNs empresariales sobre una red compartida:

- **VRF (Virtual Routing and Forwarding)**: tabla de routing separada por cliente.
- **PE (Provider Edge)**: router de borde del operador, conecta con el cliente.
- **CE (Customer Edge)**: router del cliente.
- **P (Provider)**: router del núcleo (solo maneja etiquetas, no ve rutas del cliente).

---

## Related Topics

- [[Sistemas-Autonomos-y-Routing]] — contexto de routing en redes de operador
- [[BGP]] — BGP se usa para distribuir rutas en MPLS VPN (MP-BGP)
- [[trafico/Ingenieria-Trafico]] — ingeniería de tráfico con MPLS-TE
