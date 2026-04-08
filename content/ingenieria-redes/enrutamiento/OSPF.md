---
title: OSPF — Open Shortest Path First
description: "Protocolo IGP de estado de enlace. Algoritmo Dijkstra, áreas, tipos de router, OSPFv2 para IPv4 y OSPFv3 para IPv6."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-interconexion-redes.md, ird-practica-ospf.md]
tags: [ospf, routing, estado-enlace, dijkstra, ingenieria-redes, universidad]
---

# OSPF — Open Shortest Path First

## Qué es OSPF

Protocolo de routing **interior (IGP)** de **estado de enlace**. Estándar abierto (RFC 2328). Es el más usado en redes empresariales y de operador.

- **OSPFv2**: para IPv4
- **OSPFv3**: para IPv6 (y también puede usarse con IPv4 con address families)

**Distancia administrativa: 110** (más confiable que RIP=120).

---

## Cómo funciona OSPF

OSPF mantiene **3 bases de datos**:

| Base de datos | Contenido | Comando |
|---------------|-----------|---------|
| **Adyacencia** | Routers vecinos con los que se ha establecido relación | `show ip ospf neighbor` |
| **Estado de enlace (LSDB)** | Mapa completo de la topología del área | `show ip ospf database` |
| **Tabla de reenvío** | Rutas calculadas por Dijkstra | `show ip route` |

**Proceso:**
1. Los routers intercambian **Hello packets** para descubrir vecinos.
2. Se establece adyacencia con los vecinos seleccionados.
3. Se intercambian **LSAs (Link State Advertisements)** hasta que todos tienen el mismo LSDB.
4. Cada router ejecuta el algoritmo **SPF (Dijkstra)** sobre el LSDB para calcular el árbol de mínimo coste.
5. Las mejores rutas se instalan en la tabla de routing.

**Métrica (coste):**
$$\text{coste} = \frac{10^8}{\text{ancho de banda (bps)}}$$

| Enlace | Ancho de banda | Coste por defecto |
|--------|---------------|-------------------|
| FastEthernet | 100 Mbps | 1 |
| GigabitEthernet | 1000 Mbps | 1 (igual que FE si no se ajusta!) |
| Serial | 1.544 Mbps | 64 |

> [!warning] El coste de GE y FE coincide por defecto
> En redes con GigabitEthernet hay que ajustar la referencia de ancho de banda: `auto-cost reference-bandwidth 1000`.

---

## Tipos de red OSPF y DR/BDR

En redes de acceso múltiple (Ethernet), OSPF elige un **Designated Router (DR)** y un **Backup DR (BDR)** para reducir el número de adyacencias:

- Todos los routers forman adyacencia con DR y BDR, no entre sí.
- El DR es el que coordina el intercambio de LSAs.
- Elección: gana el de mayor **prioridad** (0-255, 0 = no puede ser DR) → en empate, gana el mayor **Router ID**.

**Router ID** = IP de loopback si existe, o la mayor IP de interfaz activa.

---

## Áreas OSPF

OSPF organiza los routers en **áreas** para limitar la propagación de LSAs:

- **Área 0 (backbone)**: obligatoria. Todas las demás áreas deben conectarse a ella.
- **Área no-backbone**: conectada al backbone mediante un ABR.
- **ABR (Area Border Router)**: router en dos áreas, resume rutas entre ellas.
- **ASBR (AS Boundary Router)**: conecta OSPF con otros protocolos de routing.

**OSPF de área única (práctica habitual en el laboratorio):** todos los routers en el Área 0.

---

## Configuración IOS

### OSPFv2 (IPv4)
```
! Activar OSPF con process-id (local, no tiene por qué coincidir entre routers)
Router(config)# router ospf 1

! Definir el Router ID explícitamente
Router(config-router)# router-id 1.1.1.1

! Anunciar redes (red, wildcard-mask, area)
Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
Router(config-router)# network 10.0.0.0 0.0.0.3 area 0

! Ajustar referencia de ancho de banda (recomendado con GE)
Router(config-router)# auto-cost reference-bandwidth 1000

! Hacer interfaz pasiva (no manda Hellos a hosts)
Router(config-router)# passive-interface GigabitEthernet0/0
```

### OSPFv3 (IPv6)
```
Router(config)# ipv6 unicast-routing
Router(config)# ipv6 router ospf 1
Router(config-rtr)# router-id 1.1.1.1

! En cada interfaz (no en el proceso global)
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ipv6 ospf 1 area 0
```

---

## Verificación

```
show ip ospf neighbor          ! Vecinos y su estado
show ip ospf database          ! LSDB (mapa de la red)
show ip route ospf             ! Rutas aprendidas por OSPF
show ip ospf interface brief   ! Estado de OSPF en interfaces
show ip protocols              ! Resumen del protocolo activo
```

---

## Related Topics

- [[Sistemas-Autonomos-y-Routing]] — contexto IGP/EGP y comparativa con RIP
- [[BGP]] — el protocolo EGP que conecta los AS entre sí
- [[Guia-Packet-Tracer]] — comandos completos y troubleshooting
