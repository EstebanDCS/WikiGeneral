---
title: HSRP — Hot Standby Router Protocol
description: "HSRP: redundancia de gateway. Virtual IP y MAC compartidas. Roles activo/standby. Priority y preempt. Configuración Cisco IOS."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-practica-hsrp.md]
tags: [hsrp, fhrp, redundancia, alta-disponibilidad, ingenieria-redes, universidad]
---

# HSRP — Hot Standby Router Protocol

## Problema que resuelve

Los hosts configuran **una sola IP de gateway**. Si el router gateway falla, pierden conectividad incluso si hay un router alternativo disponible.

**FHRP (First Hop Redundancy Protocol)** soluciona esto creando un **gateway virtual** compartido por varios routers.

---

## Cómo funciona HSRP

HSRP (RFC 2281, Cisco propietario) crea:
- Una **IP virtual** compartida → los hosts la usan como gateway
- Una **MAC virtual** (`0000.0C07.ACxx` donde `xx` es el grupo) → se responde a ARP con esta MAC

Los routers HSRP tienen roles:
- **Activo**: procesa el tráfico enviado a la IP/MAC virtual. Solo hay uno.
- **Standby**: monitoriza al activo. Toma el rol activo si el activo falla.
- **Otros**: en espera, candidatos a standby.

**Elección del router activo:**
1. Mayor **prioridad** (0-255, defecto=100) → gana.
2. En empate → mayor **IP de interfaz**.

---

## Mensajes Hello

Los routers intercambian mensajes **Hello** cada 3 segundos (defecto). Si el activo no envía Hello en **10 segundos** (hold time), el standby toma el rol activo.

---

## Configuración

```
! R1 — será el activo (prioridad mayor)
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip address 192.168.1.2 255.255.255.0
R1(config-if)# standby 1 ip 192.168.1.1         ! IP virtual
R1(config-if)# standby 1 priority 110            ! mayor prioridad → activo
R1(config-if)# standby 1 preempt                ! recupera el rol activo al reconectarse
R1(config-if)# no shutdown

! R2 — será el standby (prioridad menor)
R2(config)# interface GigabitEthernet0/0
R2(config-if)# ip address 192.168.1.3 255.255.255.0
R2(config-if)# standby 1 ip 192.168.1.1
R2(config-if)# standby 1 priority 90
R2(config-if)# standby 1 preempt
R2(config-if)# no shutdown
```

**Los clientes configuran `192.168.1.1` como gateway.** Esta IP siempre responde, independientemente de qué router esté activo.

---

## El parámetro `preempt`

Sin `preempt`: cuando el router activo original vuelve después de un fallo, **no recupera** automáticamente el rol activo (lo mantiene el que fue standby). Con `preempt`: si el router con mayor prioridad vuelve, **sí recupera** el rol activo.

---

## Grupos HSRP

Se pueden tener múltiples grupos (0-255) por interfaz. Esto permite:
- Balanceo de carga: VLAN 10 usa R1 como activo, VLAN 20 usa R2 como activo.
- Cada grupo tiene su propia IP virtual.

```
! R1: activo para grupo 1, standby para grupo 2
R1(config-if)# standby 1 ip 192.168.1.1
R1(config-if)# standby 1 priority 110
R1(config-if)# standby 2 ip 192.168.1.2
R1(config-if)# standby 2 priority 90
```

---

## Verificación

```
R1# show standby                    ! estado detallado de todos los grupos
R1# show standby brief              ! resumen en tabla

! Salida típica:
!   Interface   Grp  Pri P State   Active          Standby         Virtual IP
!   Gi0/0       1    110 P Active  local           192.168.1.3     192.168.1.1
```

---

## Alternativas FHRP

| Protocolo | Estándar | Notas |
|-----------|----------|-------|
| **HSRP** | Cisco propietario | Más común en Cisco |
| **VRRP** (RFC 5798) | Estándar IEEE | Muy similar a HSRP |
| **GLBP** | Cisco propietario | Añade balanceo de carga real |

---

## Related Topics

- [[VLANs-y-Conmutacion]] — HSRP por VLAN para balanceo
- [[Guia-Packet-Tracer]] — comandos HSRP en IOS
- [[Sistemas-Autonomos-y-Routing]] — routing y disponibilidad
