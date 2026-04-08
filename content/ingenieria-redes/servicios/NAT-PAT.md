---
title: NAT / PAT — Traducción de Direcciones
description: "NAT estático, dinámico y PAT (overload). Rangos privados RFC 1918. Configuración Cisco IOS. Problemas y limitaciones."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-practicas.md, ird-sesion-natpat.md]
tags: [nat, pat, direccionamiento, privado, ingenieria-redes, universidad]
---

# NAT / PAT — Traducción de Direcciones

## Por qué existe NAT

El agotamiento de IPv4 obligó a usar **direcciones privadas** (RFC 1918) en redes internas y **traducciones** al salir a Internet.

**Rangos de direcciones privadas (no ruteables en Internet):**

| Rango | Clase | Hosts |
|-------|-------|-------|
| `10.0.0.0/8` | A | ~16 millones |
| `172.16.0.0/12` | B | ~1 millón |
| `192.168.0.0/16` | C | ~65.000 |

---

## Tipos de NAT

### NAT estático (static NAT)

Mapeo **fijo** uno a uno entre una IP privada y una IP pública.

```
R1(config)# ip nat inside source static 192.168.1.100 203.0.113.5
```

Útil para servidores internos accesibles desde Internet (web, FTP...).

### NAT dinámico

Pool de IPs públicas. Cada conexión consume una IP del pool mientras dura.

```
R1(config)# ip nat pool PUBLICAS 203.0.113.1 203.0.113.10 netmask 255.255.255.0
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255
R1(config)# ip nat inside source list 1 pool PUBLICAS
```

Si el pool se agota, nuevas conexiones son descartadas.

### PAT — Port Address Translation (NAT con sobrecarga)

**Muchas IPs privadas → 1 IP pública**, diferenciadas por número de puerto TCP/UDP.

```
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255
R1(config)# ip nat inside source list 1 interface Serial0/0/0 overload
```

Es el tipo más común. Toda la red doméstica sale a Internet con una sola IP pública.

---

## Configuración completa

**Paso 1:** Identificar interfaces inside/outside.

```
R1(config)# interface GigabitEthernet0/0    ! red interna
R1(config-if)# ip nat inside

R1(config)# interface Serial0/0/0           ! salida a Internet
R1(config-if)# ip nat outside
```

**Paso 2:** Definir qué IPs se traducen (ACL) y cómo (pool o interface).

**Paso 3:** Verificar.

```
R1# show ip nat translations         ! tabla de traducciones activas
R1# show ip nat statistics           ! contadores
R1# clear ip nat translation *       ! vaciar tabla
```

---

## Problemas de NAT

| Problema | Descripción |
|----------|-------------|
| **Rompe end-to-end** | Los hosts internos no son alcanzables directamente desde Internet |
| **Complica protocolos** | FTP activo, SIP, IPsec necesitan Application Layer Gateway (ALG) |
| **Estado** | El router debe mantener tabla de traducciones; punto de fallo |
| **Latencia** | Overhead de traducción por paquete |

> NAT es una solución temporal. IPv6 elimina la necesidad de NAT al ofrecer IPs públicas a todos los dispositivos.

---

## Related Topics

- [[ACL]] — las ACLs se usan para definir qué tráfico se traduce
- [[IPv6]] — sin NAT en IPv6
- [[Guia-Packet-Tracer]] — comandos NAT en IOS
- [[Arquitectura-Internet]] — agotamiento IPv4 y por qué NAT existe
