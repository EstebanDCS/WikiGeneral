---
title: Subredes y CIDR
description: "División de redes en subredes. CIDR, VLSM, máscaras, wildcard masks. Cálculo de subredes IPv4. Direcciones de red y broadcast."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-practicas.md, ird-interconexion-redes.md]
tags: [subredes, cidr, vlsm, direccionamiento, ipv4, ingenieria-redes, universidad]
---

# Subredes y CIDR

## Direccionamiento IPv4

Una dirección IPv4 son **32 bits** en cuatro octetos decimales: `192.168.1.10`.

La **máscara de subred** divide la dirección en:
- **Parte de red** (bits con 1 en la máscara) — identifica la red
- **Parte de host** (bits con 0 en la máscara) — identifica el host dentro de la red

```
IP:     192.168.1.10  →  11000000.10101000.00000001.00001010
Máscara: 255.255.255.0 →  11111111.11111111.11111111.00000000
                          ←────────── red ──────────→ ←host→
```

---

## CIDR — Classless Inter-Domain Routing

**Notación CIDR:** `192.168.1.0/24` — el `/24` indica que los primeros 24 bits son la parte de red.

Antes de CIDR existían clases fijas:

| Clase | Primer octeto | Máscara por defecto | Hosts |
|-------|---------------|---------------------|-------|
| A | 1–126 | /8 (255.0.0.0) | ~16M |
| B | 128–191 | /16 (255.255.0.0) | ~65K |
| C | 192–223 | /24 (255.255.255.0) | 254 |

CIDR elimina estas clases — cualquier prefijo puede tener cualquier longitud de máscara.

---

## Cálculo de subredes

Para una red `192.168.1.0/24` dividida en subredes `/26`:

- Bits de subred: `26 - 24 = 2` bits → $2^2 = 4$ subredes
- Hosts por subred: $2^{32-26} = 2^6 = 64$ → **62 hosts** usables (se restan dirección de red y broadcast)

| Subred | Red | Primer host | Último host | Broadcast |
|--------|-----|-------------|-------------|-----------|
| /26 #1 | 192.168.1.0 | .1 | .62 | .63 |
| /26 #2 | 192.168.1.64 | .65 | .126 | .127 |
| /26 #3 | 192.168.1.128 | .129 | .190 | .191 |
| /26 #4 | 192.168.1.192 | .193 | .254 | .255 |

**Fórmulas:**
- Número de subredes: $2^{\text{bits tomados}}$
- Hosts usables por subred: $2^{\text{bits de host}} - 2$
- Dirección de broadcast: poner todos los bits de host a 1.

---

## VLSM — Variable Length Subnet Mask

VLSM permite usar **máscaras de diferente longitud** para distintas subredes, optimizando el uso del espacio.

**Ejemplo:** Red `10.0.0.0/24` con necesidades:
- LAN A: 100 hosts → `/25` (126 hosts)
- LAN B: 50 hosts → `/26` (62 hosts)
- Enlace WAN: 2 hosts → `/30` (2 hosts)

```
10.0.0.0/25   → LAN A   (10.0.0.1 – 10.0.0.126)
10.0.0.128/26 → LAN B   (10.0.0.129 – 10.0.0.190)
10.0.0.192/30 → WAN     (10.0.0.193 – 10.0.0.194)
```

> RIPv1 no soporta VLSM (no envía la máscara). RIPv2 y OSPF sí.

---

## Wildcard Mask

Inverso de la máscara de subred. Usada en ACLs y OSPF:

```
Máscara: 255.255.255.0  →  Wildcard: 0.0.0.255
Máscara: 255.255.0.0    →  Wildcard: 0.0.255.255
Máscara: 255.255.255.252→  Wildcard: 0.0.0.3
```

- Bit `0` → debe coincidir
- Bit `1` → no importa

---

## Prefijos comunes

| CIDR | Máscara | Hosts usables |
|------|---------|---------------|
| /30 | 255.255.255.252 | 2 (enlaces WAN) |
| /29 | 255.255.255.248 | 6 |
| /28 | 255.255.255.240 | 14 |
| /27 | 255.255.255.224 | 30 |
| /26 | 255.255.255.192 | 62 |
| /25 | 255.255.255.128 | 126 |
| /24 | 255.255.255.0 | 254 |
| /16 | 255.255.0.0 | 65.534 |

---

## Related Topics

- [[Arquitectura-Internet]] — direccionamiento MAC vs IP
- [[OSPF]] — usa wildcard masks en el comando `network`
- [[ACL]] — wildcard masks en reglas de acceso
- [[NAT-PAT]] — rangos de direcciones privadas RFC 1918
