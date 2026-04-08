---
title: Guía Cisco IOS y Packet Tracer
description: "Referencia completa de comandos Cisco IOS para todas las prácticas: modos CLI, interfaces, routing estático/dinámico, VLANs, IPv6, DHCP, NAT/PAT, ACL, HSRP, EtherChannel, OSPF."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-practicas.md, ird-practica-ospf.md, ird-practica-dhcp.md, ird-practica-hsrp.md, ird-sesion-acl.md, ird-sesion-natpat.md]
tags: [packet-tracer, cisco-ios, cli, routing, ingenieria-redes, universidad, practicas]
---

# Guía Cisco IOS y Packet Tracer

## Modos CLI — Navegación

```
Router>              ← modo usuario (solo lectura básica)
Router> enable       ← pasar a modo privilegiado
Router#              ← modo privilegiado (show, copy, reload…)
Router# configure terminal
Router(config)#      ← modo configuración global
Router(config)# interface GigabitEthernet0/0
Router(config-if)#   ← modo configuración de interfaz
Router(config-if)# exit          ← volver un nivel
Router(config-if)# end           ← volver directamente a #
```

**Atajos de teclado:**
- `Tab` → autocompletar comando único
- `?` → mostrar opciones disponibles
- `Ctrl+C` → salir del modo config
- Comandos pueden abreviarse: `conf t`, `int g0/0`, `no sh`

---

## Gestión de configuración

```
! Ver configuración activa (RAM)
Router# show running-config

! Ver configuración de arranque (NVRAM)
Router# show startup-config

! Guardar en NVRAM (persiste tras reinicio)
Router# copy running-config startup-config
Router# write memory            ! alternativa

! Borrar configuración de arranque
Router# erase startup-config
Router# reload                  ! reiniciar equipo

! Información del sistema
Router# show version
Router# show history            ! comandos anteriores
```

**Tipos de memoria:**

| Memoria | Contenido | Volátil |
|---------|-----------|---------|
| DRAM | running-config | Sí (se borra al reiniciar) |
| NVRAM | startup-config | No |
| Flash | imagen IOS | No |
| ROM | boot mínimo | No |

---

## Configuración básica del equipo

```
! Hostname y contraseñas
Router(config)# hostname R1
R1(config)# enable secret cisco123        ! contraseña modo #
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# line vty 0 4                  ! acceso SSH/Telnet
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# transport input ssh telnet

! Banner de aviso
R1(config)# banner motd # Acceso restringido #

! Desactivar resolución DNS (evita esperas)
R1(config)# no ip domain-lookup

! Sincronizar logs con comandos
R1(config)# line console 0
R1(config-line)# logging synchronous
```

---

## Interfaces — Configuración IPv4

```
! Interfaz Ethernet (router)
R1(config)# interface GigabitEthernet0/0
R1(config-if)# description Enlace a LAN
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown               ! activar interfaz

! Interfaz Serial (DCE — proporciona reloj)
R1(config)# interface Serial0/0/0
R1(config-if)# ip address 10.0.0.1 255.255.255.252
R1(config-if)# clock rate 64000          ! solo en extremo DCE
R1(config-if)# no shutdown

! Interfaz Serial (DTE — sin clock rate)
R1(config)# interface Serial0/0/0
R1(config-if)# ip address 10.0.0.2 255.255.255.252
R1(config-if)# no shutdown
```

**Verificación de interfaces:**
```
show ip interface brief          ! resumen estado + IP
show interfaces GigabitEthernet0/0   ! estadísticas detalladas
show interfaces status           ! en switches
```

---

## Nomenclatura de interfaces (Packet Tracer)

| Tipo | Abreviatura | Ejemplo |
|------|-------------|---------|
| GigabitEthernet | `g` o `Gi` | `GigabitEthernet0/0` |
| FastEthernet | `f` o `Fa` | `FastEthernet0/1` |
| Serial | `s` o `Se` | `Serial0/0/0` |
| Loopback | `lo` | `Loopback0` |

> En Packet Tracer los módulos empiezan en 0: la primera interfaz es `GigabitEthernet0/0`, la segunda `GigabitEthernet0/1`, etc.

---

## Routing estático

```
! ip route <red_destino> <máscara> <nexthop|interfaz>
R1(config)# ip route 192.168.2.0 255.255.255.0 10.0.0.2
R1(config)# ip route 192.168.3.0 255.255.255.0 Serial0/0/1

! Ruta por defecto (default route)
R1(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1

! Ver tabla de rutas
R1# show ip route
! Prefijos: C=Conectado, S=Estático, R=RIP, O=OSPF, B=BGP
```

---

## Routing dinámico — RIP

```
R1(config)# router rip
R1(config-router)# version 2
R1(config-router)# network 192.168.1.0
R1(config-router)# network 10.0.0.0
R1(config-router)# no auto-summary       ! necesario con VLSM
R1(config-router)# passive-interface GigabitEthernet0/0   ! no envía Hellos

R1# show ip route rip
R1# show ip protocols
```

---

## Routing dinámico — OSPF

```
! OSPFv2 (IPv4)
R1(config)# router ospf 1                ! process-id local
R1(config-router)# router-id 1.1.1.1    ! recomendado explícito
R1(config-router)# network 192.168.1.0 0.0.0.255 area 0
R1(config-router)# network 10.0.0.0 0.0.0.3 area 0
R1(config-router)# auto-cost reference-bandwidth 1000   ! ajustar para GE
R1(config-router)# passive-interface GigabitEthernet0/0

! OSPFv3 (IPv6) — configuración por interfaz
R1(config)# ipv6 router ospf 1
R1(config-rtr)# router-id 1.1.1.1
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ipv6 ospf 1 area 0

! Verificación OSPF
show ip ospf neighbor              ! vecinos y estado
show ip ospf database              ! LSDB completa
show ip route ospf                 ! rutas aprendidas
show ip ospf interface brief       ! OSPF por interfaz
show ip protocols                  ! resumen protocolos
```

---

## Conmutación — Switch básico

```
! No se necesita configuración para forwarding básico

! Ver tabla MAC
Switch# show mac-address-table

! Limpiar tabla MAC
Switch# clear mac-address-table dynamic

! Información de interfaces
Switch# show interfaces FastEthernet0/1
Switch# show interfaces status

! CDP (descubrimiento de vecinos)
Switch# show cdp neighbors
Switch# show cdp neighbors detail
```

---

## VLANs

```
! Crear VLANs
Switch(config)# vlan 10
Switch(config-vlan)# name VENTAS
Switch(config)# vlan 20
Switch(config-vlan)# name RRHH

! Asignar puerto de acceso a VLAN
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10

! Configurar trunk (enlace entre switches o a router)
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan all   ! por defecto

! Verificación VLANs
Switch# show vlan brief
Switch# show interfaces trunk
Switch# show interfaces GigabitEthernet0/1 switchport
```

### Inter-VLAN routing — Router-on-a-stick

```
! En el router: subinterfaces para cada VLAN
R1(config)# interface GigabitEthernet0/0.10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip address 192.168.10.1 255.255.255.0

R1(config)# interface GigabitEthernet0/0.20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip address 192.168.20.1 255.255.255.0

! Activar la interfaz física principal
R1(config)# interface GigabitEthernet0/0
R1(config-if)# no shutdown
```

### Inter-VLAN routing — Switch L3

```
! En switch capa 3: SVI (Switched Virtual Interface)
SW(config)# ip routing                   ! habilitar routing
SW(config)# interface vlan 10
SW(config-if)# ip address 192.168.10.1 255.255.255.0
SW(config-if)# no shutdown
SW(config)# interface vlan 20
SW(config-if)# ip address 192.168.20.1 255.255.255.0
SW(config-if)# no shutdown
```

---

## STP (Spanning Tree Protocol)

```
! Ver estado de STP
Switch# show spanning-tree
Switch# show spanning-tree vlan 10

! Cambiar prioridad (para forzar root bridge)
Switch(config)# spanning-tree vlan 10 priority 4096    ! múltiplo de 4096
Switch(config)# spanning-tree vlan 10 root primary     ! automático

! PortFast (puertos a hosts — omite estados STP)
Switch(config)# interface FastEthernet0/5
Switch(config-if)# spanning-tree portfast

! Estados STP: Blocking → Listening → Learning → Forwarding → Disabled
```

---

## EtherChannel

```
! LACP (estándar 802.3ad) — recomendado
Switch(config)# interface range FastEthernet0/1-2
Switch(config-if-range)# channel-group 1 mode active   ! LACP activo
Switch(config-if-range)# exit

Switch(config)# interface port-channel 1
Switch(config-if)# switchport mode trunk

! PAgP (Cisco propietario)
Switch(config)# interface range FastEthernet0/1-2
Switch(config-if-range)# channel-group 1 mode desirable   ! PAgP

! Verificación
Switch# show etherchannel summary
Switch# show etherchannel port-channel
Switch# show interfaces port-channel 1
```

---

## IPv6 en interfaces

```
! Habilitar routing IPv6
R1(config)# ipv6 unicast-routing

! Asignar dirección IPv6
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ipv6 address 2001:DB8:1:1::1/64
R1(config-if)# no shutdown

! Dirección link-local manual
R1(config-if)# ipv6 address FE80::1 link-local

! Verificación IPv6
R1# show ipv6 interface brief
R1# show ipv6 route
R1# ping ipv6 2001:DB8:1:2::1
R1# traceroute ipv6 2001:DB8:1:2::1
```

---

## DHCP

```
! Configurar servidor DHCP en el router
R1(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10   ! reservar IPs
R1(config)# ip dhcp pool LAN_POOL
R1(dhcp-config)# network 192.168.1.0 255.255.255.0
R1(dhcp-config)# default-router 192.168.1.1
R1(dhcp-config)# dns-server 8.8.8.8
R1(dhcp-config)# lease 7                      ! días

! DHCP Relay — reenviar peticiones a servidor remoto
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip helper-address 10.0.0.100   ! IP del servidor DHCP

! Verificación DHCP
R1# show ip dhcp pool
R1# show ip dhcp binding              ! IPs asignadas + MACs
R1# show ip dhcp server statistics

! En cliente: renovar lease
PC> ipconfig /renew                   ! Windows
```

---

## NAT / PAT

```
! Paso 1: definir interfaces inside y outside
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip nat inside
R1(config)# interface Serial0/0/0
R1(config-if)# ip nat outside

! NAT estático (1 privada ↔ 1 pública fija)
R1(config)# ip nat inside source static 192.168.1.100 203.0.113.5

! NAT dinámico (pool de IPs públicas)
R1(config)# ip nat pool PUBLICAS 203.0.113.1 203.0.113.10 netmask 255.255.255.0
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255
R1(config)# ip nat inside source list 1 pool PUBLICAS

! PAT / NAT con sobrecarga (muchos → 1 IP pública)
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255
R1(config)# ip nat inside source list 1 interface Serial0/0/0 overload

! Verificación NAT
R1# show ip nat translations
R1# show ip nat statistics
R1# clear ip nat translation *        ! limpiar tabla NAT
```

---

## ACL — Listas de Control de Acceso

### ACL estándar (filtra solo por IP origen)

```
! Numerada (1-99): colocar CERCA DEL DESTINO
R1(config)# access-list 10 permit 192.168.1.0 0.0.0.255
R1(config)# access-list 10 deny any              ! implícito al final

! Aplicar en interfaz
R1(config)# interface GigabitEthernet0/1
R1(config-if)# ip access-group 10 out            ! tráfico saliente

! ACL nombrada estándar
R1(config)# ip access-list standard PERMIT_LAN
R1(config-std-nacl)# permit 192.168.1.0 0.0.0.255
R1(config-std-nacl)# deny any
```

### ACL extendida (filtra IP origen/destino + protocolo + puerto)

```
! Numerada (100-199): colocar CERCA DEL ORIGEN
! Formato: access-list <id> {permit|deny} <proto> <src> <wc> <dst> <wc> [eq <puerto>]
R1(config)# access-list 100 permit tcp 192.168.1.0 0.0.0.255 any eq 80
R1(config)# access-list 100 permit tcp 192.168.1.0 0.0.0.255 any eq 443
R1(config)# access-list 100 deny ip any any

! Aplicar en interfaz — near source → inbound
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip access-group 100 in

! ACL nombrada extendida
R1(config)# ip access-list extended FILTRO_WEB
R1(config-ext-nacl)# permit tcp any any eq 80
R1(config-ext-nacl)# permit tcp any any eq 443
R1(config-ext-nacl)# deny ip any any

! Verificación ACL
R1# show access-lists
R1# show ip interface GigabitEthernet0/0   ! ver ACL aplicada
```

**Reglas clave:**
- ACL estándar → cerca del **destino** (filtra solo origen)
- ACL extendida → cerca del **origen** (filtra origen+destino+puerto)
- Al final de toda ACL hay un `deny any` implícito
- Las reglas se evalúan en orden — la primera coincidencia gana
- Wildcard: inverso de la máscara (`255.255.255.0` → `0.0.0.255`)

---

## HSRP — Alta disponibilidad de router

```
! Configurar HSRP en dos routers para la misma subred
! R1 (activo - mayor prioridad)
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip address 192.168.1.2 255.255.255.0
R1(config-if)# standby 1 ip 192.168.1.1       ! IP virtual compartida
R1(config-if)# standby 1 priority 110          ! defecto=100; mayor → activo
R1(config-if)# standby 1 preempt              ! recupera rol activo si vuelve

! R2 (standby - prioridad menor)
R2(config)# interface GigabitEthernet0/0
R2(config-if)# ip address 192.168.1.3 255.255.255.0
R2(config-if)# standby 1 ip 192.168.1.1
R2(config-if)# standby 1 priority 90
R2(config-if)# standby 1 preempt

! Los clientes usan 192.168.1.1 como gateway

! Verificación HSRP
R1# show standby
R1# show standby brief
```

---

## Loopback — Interfaces virtuales

```
! Las loopbacks siempre están activas (no necesitan no shutdown)
R1(config)# interface Loopback0
R1(config-if)# ip address 1.1.1.1 255.255.255.255

! Uso típico: Router ID de OSPF, BGP, gestión
```

---

## Diagnóstico y troubleshooting

```
! Conectividad
ping 192.168.1.1
ping 192.168.1.1 source 10.0.0.1   ! ping desde interfaz específica
traceroute 192.168.2.1

! Rutas
show ip route
show ip route 192.168.2.0          ! ruta específica
show ip route summary

! Interfaces
show interfaces                     ! todas las interfaces
show ip interface brief             ! resumen rápido
show interfaces GigabitEthernet0/0  ! una interfaz

! Protocolos
show ip protocols                   ! protocolos activos
show ip ospf neighbor               ! vecinos OSPF
show ip ospf database               ! LSDB
show ip rip database                ! base RIP

! Switching
show vlan brief                     ! VLANs configuradas
show spanning-tree                  ! STP
show mac-address-table              ! tabla MAC
show etherchannel summary           ! EtherChannel

! NAT/ACL/DHCP
show ip nat translations
show access-lists
show ip dhcp binding

! Debug (usar con precaución en producción)
debug ip routing                    ! cambios en tabla rutas
debug ip ospf events                ! eventos OSPF
no debug all                        ! desactivar todos los debugs
```

---

## Flujo típico de configuración en prácticas

1. **Configurar hostname y contraseñas** en cada equipo
2. **Asignar IPs** a todas las interfaces → `no shutdown`
3. **Configurar protocolo de routing** (estático o dinámico)
4. **Verificar conectividad**: `ping`, `show ip route`
5. **Añadir servicios** (DHCP, NAT, ACL) según el escenario
6. **Guardar**: `copy running-config startup-config`

---

## Acceso a CLI en Packet Tracer

- Click en el dispositivo → pestaña **CLI** (acceso directo)
- O: conectar un **PC** al puerto de consola con cable **Console** (azul rollover) → abrir Terminal en el PC → `9600 bps, 8N1`

> El switch/router no necesita IP para configurarse por consola. La IP es solo para gestión remota.

---

## Related Topics

- [[OSPF]] — configuración detallada de OSPF
- [[VLANs-y-Conmutacion]] — VLANs, STP, EtherChannel
- [[NAT-PAT]] — NAT estático, dinámico, PAT
- [[ACL]] — diseño y aplicación de listas de acceso
- [[DHCP]] — servidor DHCP y relay
- [[HSRP]] — redundancia de gateway
- [[IPv6]] — configuración IPv6 en IOS
