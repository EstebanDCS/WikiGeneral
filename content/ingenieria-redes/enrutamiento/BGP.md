---
title: BGP — Border Gateway Protocol
description: "BGP: protocolo EGP de vector de ruta entre sistemas autónomos. eBGP vs iBGP, atributos, decisión de ruta, AS path."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-interconexion-redes.md]
tags: [bgp, routing, egp, as, internet, ingenieria-redes, universidad]
---

# BGP — Border Gateway Protocol

## Qué es BGP

**BGP-4** (RFC 4271) es el único protocolo **EGP** en uso en Internet. Conecta los **Sistemas Autónomos (AS)** entre sí y forma la "pegamento" que mantiene unido Internet.

- Protocolo de **vector de ruta (path vector)** — propaga el camino completo de ASes.
- Usa **TCP puerto 179** como transporte (confiable).
- **No converge rápido** — diseñado para estabilidad, no velocidad.
- **Distancia administrativa: 20** (eBGP) / **200** (iBGP).

---

## eBGP vs iBGP

| | **eBGP** (external) | **iBGP** (internal) |
|--|---------------------|---------------------|
| Entre | ASes diferentes | Routers del mismo AS |
| AS Path | Se añade el AS propio | No se modifica |
| TTL por defecto | 1 (vecinos directos) | 255 |
| Propagación | Sí (a vecinos eBGP e iBGP) | No a otros iBGP (regla split-horizon) |

**Regla split-horizon de iBGP:** un router no anuncia a peers iBGP rutas aprendidas de otros peers iBGP. Solución: **full mesh** (todos conectados con todos) o **Route Reflector**.

---

## Atributos BGP

BGP selecciona la mejor ruta usando atributos (en orden de preferencia):

1. **Weight** (Cisco local, no se propaga) — mayor = mejor
2. **Local Preference** — mayor = mejor (dentro del AS)
3. **Locally originated** — ruta originada localmente gana
4. **AS Path length** — menor número de ASes = mejor
5. **Origin** (IGP < EGP < Incomplete)
6. **MED** (Multi-Exit Discriminator) — menor = mejor
7. **eBGP sobre iBGP**
8. **IGP metric** hacia el next-hop

---

## AS Path

El atributo más importante para evitar bucles. Cada AS que propaga una ruta **añade su ASN** al path. Si un router recibe una ruta con su propio AS en el path, la descarta (loop prevention).

```
AS 100 → AS 200 → AS 300:  AS_PATH = "300 200 100"
```

---

## Configuración básica (referencia)

```
! Configurar BGP en el AS 100
R1(config)# router bgp 100
R1(config-router)# bgp router-id 1.1.1.1
R1(config-router)# neighbor 203.0.113.2 remote-as 200   ! peer eBGP
R1(config-router)# network 192.168.1.0 mask 255.255.255.0  ! anunciar red

! Verificación BGP
R1# show bgp summary
R1# show bgp
R1# show ip bgp neighbors
```

> BGP no se configura en las prácticas de laboratorio de esta asignatura, pero es fundamental para entender cómo funciona Internet.

---

## BGP en Internet

- ~900.000 prefijos en la tabla BGP global (2024).
- ~75.000 ASes activos.
- Tier 1 ISPs se intercambian rutas por BGP sin pago (peering).
- Tier 2/3 ISPs compran tránsito (transit) a ISPs superiores.

---

## Related Topics

- [[Sistemas-Autonomos-y-Routing]] — contexto AS, IGP vs EGP
- [[Arquitectura-Internet]] — jerarquía de Internet, peering y transit
- [[OSPF]] — protocolo IGP que usa BGP internamente para next-hop resolution
