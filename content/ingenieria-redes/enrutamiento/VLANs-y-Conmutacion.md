---
title: VLANs y Conmutación LAN
description: "Switches, tabla MAC, STP, VLANs, trunking 802.1Q, inter-VLAN routing (router-on-a-stick y switch L3), EtherChannel."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-practicas.md, ird-interconexion-redes.md]
tags: [vlan, switching, stp, trunking, interconexion, ingenieria-redes, universidad]
---

# VLANs y Conmutación LAN

## Switch — Funcionamiento básico

Un **switch LAN** opera en capa 2 (Ethernet). Su función es reenviar tramas al puerto correcto usando la **tabla de direcciones MAC**.

**Aprendizaje hacia atrás (backward learning):**
1. Llega una trama → el switch anota la MAC origen + puerto en su tabla.
2. Si la MAC destino **no está** en la tabla → **flooding** (reenvía por todos los puertos excepto el de entrada).
3. Si la MAC destino **sí está** → reenvía solo por ese puerto (**filtering**).

**Dominio de colisión:** cada puerto del switch es un dominio independiente. El **dominio de broadcast** sigue siendo toda la LAN (una VLAN = un dominio de broadcast).

---

## STP — Spanning Tree Protocol (802.1D)

En topologías redundantes (bucles físicos), el broadcast loop colapsaría la red. STP elimina los bucles lógicamente bloqueando puertos.

**Proceso de elección:**
1. **Root Bridge**: el switch con menor **Bridge ID** (prioridad 0-65535 + MAC). Por defecto prioridad = 32768.
2. Desde el Root Bridge, cada switch calcula el **puerto raíz** (menor coste hacia root).
3. En cada segmento, se elige un **puerto designado** (el de menor coste hacia root).
4. Los demás puertos quedan en estado **blocking**.

**Estados de un puerto STP:**

| Estado | Acción |
|--------|--------|
| **Blocking** | No reenvía tramas (solo escucha BPDUs) |
| **Listening** | Procesa BPDUs, no reenvía |
| **Learning** | Aprende MACs, no reenvía |
| **Forwarding** | Reenvía tramas normalmente |
| **Disabled** | Desactivado por el administrador |

**Convergencia:** STP tarda ~30-50 s en converger (Blocking→Listening 15s→Learning 15s→Forwarding).

**RSTP (802.1w):** versión rápida, converge en segundos. **PortFast**: omite estados en puertos de acceso conectados a hosts.

```
! Configuración STP
Switch(config)# spanning-tree vlan 10 priority 4096          ! forzar root
Switch(config)# spanning-tree vlan 10 root primary           ! automático
Switch(config-if)# spanning-tree portfast                    ! puertos de hosts

Switch# show spanning-tree
Switch# show spanning-tree vlan 10
```

---

## VLANs — Virtual LANs

Una **VLAN** es una segmentación lógica de la red en capa 2. Equipos en diferentes VLANs no pueden comunicarse directamente (necesitan routing de capa 3).

**Ventajas:**
- Segmentación del tráfico de broadcast
- Mayor seguridad (aislamiento entre departamentos)
- Flexibilidad sin necesidad de recableado

### Tipos de puertos

| Tipo | Uso | Tráfico |
|------|-----|---------|
| **Acceso (access)** | Conecta a hosts | Sin etiqueta VLAN |
| **Troncal (trunk)** | Entre switches, switch-router | Con etiqueta 802.1Q |

### Configuración en Cisco IOS

```
! Crear VLANs
Switch(config)# vlan 10
Switch(config-vlan)# name VENTAS
Switch(config)# vlan 20
Switch(config-vlan)# name RRHH

! Puerto de acceso
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10

! Puerto troncal (802.1Q)
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk native vlan 1     ! VLAN nativa sin etiquetar
Switch(config-if)# switchport trunk allowed vlan 10,20  ! solo estas VLANs

Switch# show vlan brief
Switch# show interfaces trunk
```

---

## Trunking — 802.1Q

El protocolo **802.1Q** añade una etiqueta de 4 bytes a cada trama Ethernet en los puertos trunk, indicando a qué VLAN pertenece.

```
[Ethernet header | 802.1Q tag (4B) | payload | FCS]
                      └─ TPID (0x8100) + PRI (3b) + VLAN ID (12b)
```

- **VLAN nativa**: VLAN que viaja sin etiquetar por el trunk. Debe coincidir en ambos extremos.
- **DTP (Dynamic Trunking Protocol)**: protocolo Cisco para negociar automáticamente el modo trunk. Se puede desactivar: `switchport nonegotiate`.

---

## Inter-VLAN Routing

Las VLANs son dominios de broadcast separados. Para comunicarlos se necesita routing L3.

### Opción 1: Router-on-a-stick

Un único enlace físico router-switch con subinterfaces por VLAN.

```
! En el switch: trunk hacia el router
Switch(config-if)# switchport mode trunk

! En el router: subinterfaces
R1(config)# interface GigabitEthernet0/0
R1(config-if)# no shutdown              ! activar la física primero

R1(config)# interface GigabitEthernet0/0.10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip address 192.168.10.1 255.255.255.0

R1(config)# interface GigabitEthernet0/0.20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip address 192.168.20.1 255.255.255.0
```

Los hosts de VLAN 10 usan `192.168.10.1` como gateway, y los de VLAN 20 usan `192.168.20.1`.

### Opción 2: Switch de capa 3 (L3)

```
SW(config)# ip routing                  ! habilitar routing IPv4

! SVI (Switched Virtual Interface) por VLAN
SW(config)# interface vlan 10
SW(config-if)# ip address 192.168.10.1 255.255.255.0
SW(config-if)# no shutdown

SW(config)# interface vlan 20
SW(config-if)# ip address 192.168.20.1 255.255.255.0
SW(config-if)# no shutdown
```

El switch L3 enruta internamente entre VLANs sin necesitar un router externo.

---

## EtherChannel

Agrupa físicamente varios enlaces paralelos en un único **canal lógico**. STP lo ve como un solo enlace, evitando el bloqueo.

- Hasta **8 puertos** por grupo
- Protocolos de negociación: **LACP** (802.3ad, estándar) y **PAgP** (Cisco propietario)
- Los puertos deben tener la misma velocidad, dúplex y configuración de VLAN

```
! LACP (recomendado)
Switch(config)# interface range FastEthernet0/1-2
Switch(config-if-range)# channel-group 1 mode active    ! LACP activo

! PAgP
Switch(config)# interface range FastEthernet0/1-2
Switch(config-if-range)# channel-group 1 mode desirable  ! PAgP

! Configurar el port-channel como trunk
Switch(config)# interface port-channel 1
Switch(config-if)# switchport mode trunk

Switch# show etherchannel summary
Switch# show etherchannel port-channel
```

**Modos LACP:** `active` (envía LACP) vs `passive` (responde). Al menos un extremo debe ser `active`.
**Modos PAgP:** `desirable` (envía PAgP) vs `auto` (responde). Al menos un extremo debe ser `desirable`.

---

<svg viewBox="0 0 520 200" width="520" height="200" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <!-- VLAN 10 hosts -->
  <rect x="20" y="30" width="60" height="28" rx="4" fill="#4a7fa5" opacity="0.7"/>
  <text x="50" y="49" font-size="10" fill="white" text-anchor="middle">PC-A VLAN10</text>
  <rect x="20" y="80" width="60" height="28" rx="4" fill="#4a7fa5" opacity="0.7"/>
  <text x="50" y="99" font-size="10" fill="white" text-anchor="middle">PC-B VLAN10</text>
  <!-- VLAN 20 hosts -->
  <rect x="20" y="140" width="60" height="28" rx="4" fill="#5ba85e" opacity="0.7"/>
  <text x="50" y="159" font-size="10" fill="white" text-anchor="middle">PC-C VLAN20</text>
  <!-- Switch -->
  <rect x="160" y="50" width="80" height="100" rx="6" fill="#888" opacity="0.3"/>
  <text x="200" y="105" font-size="11" fill="currentColor" text-anchor="middle" font-weight="bold">Switch</text>
  <!-- Router -->
  <rect x="360" y="75" width="80" height="50" rx="6" fill="#c0582a" opacity="0.4"/>
  <text x="400" y="105" font-size="11" fill="currentColor" text-anchor="middle" font-weight="bold">Router</text>
  <!-- Lines -->
  <line x1="80" y1="44" x2="160" y2="80" stroke="#4a7fa5" stroke-width="1.5"/>
  <line x1="80" y1="94" x2="160" y2="100" stroke="#4a7fa5" stroke-width="1.5"/>
  <line x1="80" y1="154" x2="160" y2="130" stroke="#5ba85e" stroke-width="1.5"/>
  <!-- Trunk -->
  <line x1="240" y1="100" x2="360" y2="100" stroke="currentColor" stroke-width="2.5" stroke-dasharray="6,3"/>
  <text x="300" y="94" font-size="9" fill="currentColor" text-anchor="middle">trunk 802.1Q</text>
  <!-- Sub interfaces -->
  <text x="400" y="65" font-size="8" fill="#4a7fa5">.10 → 192.168.10.1</text>
  <text x="400" y="140" font-size="8" fill="#5ba85e">.20 → 192.168.20.1</text>
</svg>

---

## Related Topics

- [[Guia-Packet-Tracer]] — comandos completos de switching
- [[OSPF]] — routing dinámico entre VLANs
- [[EtherChannel-STP]] — configuración avanzada EtherChannel y STP
- [[Sistemas-Autonomos-y-Routing]] — conceptos de routing
