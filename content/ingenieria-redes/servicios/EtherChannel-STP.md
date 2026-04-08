---
title: EtherChannel y STP
description: "EtherChannel (LACP/PAgP): agregación de enlaces. STP (802.1D/RSTP): eliminación de bucles, elección root bridge, estados de puertos."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-practica-portchannel.md, ird-practicas.md]
tags: [etherchannel, stp, spanning-tree, lacp, pagp, ingenieria-redes, universidad]
---

# EtherChannel y STP

## STP — Spanning Tree Protocol (802.1D)

### El problema: bucles de capa 2

En redes con redundancia física (varios cables entre switches), los **frames de broadcast** circulan indefinidamente creando tormentas de broadcast que colapsan la red. STP resuelve esto **bloqueando puertos** para crear una topología lógica sin bucles.

### Funcionamiento

STP intercambia **BPDUs (Bridge Protocol Data Units)** entre switches para:

1. **Elegir el Root Bridge**: el switch con menor **Bridge ID** (prioridad + MAC). Actúa como raíz del árbol.
2. **Calcular el árbol**: cada switch encuentra el camino de menor coste hacia el Root Bridge.
3. **Bloquear puertos redundantes**: los que crearían bucles quedan en estado Blocking.

**Bridge ID:** 2 bytes de prioridad (0-65535, defecto 32768) + 6 bytes de MAC. Menor = mejor.

**Coste STP:**

| Velocidad | Coste IEEE |
|-----------|-----------|
| 10 Mbps | 100 |
| 100 Mbps | 19 |
| 1 Gbps | 4 |
| 10 Gbps | 2 |

### Estados de un puerto

```
Disabled → Blocking → Listening (15s) → Learning (15s) → Forwarding
```

| Estado | Envía tramas | Aprende MACs | Procesa BPDUs |
|--------|-------------|--------------|---------------|
| Blocking | No | No | Sí |
| Listening | No | No | Sí |
| Learning | No | Sí | Sí |
| Forwarding | Sí | Sí | Sí |

**Convergencia STP (802.1D):** ~30-50 segundos.

### RSTP — Rapid Spanning Tree (802.1w)

Converge en ~1-2 segundos. Introduce:
- **Edge ports** (PortFast): puertos a hosts, pasan directamente a Forwarding.
- **Alternate ports**: alternativa precomputed al puerto raíz.
- **Backup ports**: alternativa dentro del mismo segmento.

### Configuración STP

```
! Forzar Root Bridge
Switch(config)# spanning-tree vlan 10 priority 4096      ! múltiplo de 4096
Switch(config)# spanning-tree vlan 10 root primary       ! elige el valor óptimo

! Root secundario (backup)
Switch(config)# spanning-tree vlan 10 root secondary

! PortFast en puertos de acceso (hosts finales)
Switch(config-if)# spanning-tree portfast

! BPDU Guard: desactiva el puerto si recibe BPDUs (protege PortFast)
Switch(config-if)# spanning-tree bpduguard enable

! Verificación
Switch# show spanning-tree
Switch# show spanning-tree vlan 10
Switch# show spanning-tree interface FastEthernet0/1
```

---

## EtherChannel

### Qué es

**EtherChannel** (también llamado Port Channel o LAG — Link Aggregation Group) agrupa varios enlaces físicos paralelos en **un único enlace lógico**:

- STP ve un solo enlace → no bloquea ningún puerto del grupo.
- Aumenta el ancho de banda (hasta 8 × velocidad de un enlace).
- Si un enlace falla, el grupo sigue funcionando con los restantes.

### Protocolos de negociación

| Protocolo | Estándar | Modo activo | Modo pasivo |
|-----------|----------|-------------|-------------|
| **LACP** (802.3ad) | IEEE (recomendado) | `active` | `passive` |
| **PAgP** | Cisco propietario | `desirable` | `auto` |
| Sin negociación | — | `on` | `on` |

Al menos un extremo debe estar en modo activo/desirable. Dos extremos en `passive`/`auto` no forman EtherChannel.

### Requisitos

Los puertos del grupo deben tener **idéntica configuración**:
- Misma velocidad y dúplex
- Mismo modo (access o trunk)
- Misma VLAN (si access) o mismas VLANs permitidas (si trunk)

### Configuración

```
! LACP — método recomendado
Switch(config)# interface range GigabitEthernet0/1-2
Switch(config-if-range)# channel-group 1 mode active      ! LACP activo
Switch(config-if-range)# exit

! Configurar el port-channel como trunk
Switch(config)# interface port-channel 1
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,30

! PAgP — alternativa Cisco
Switch(config)# interface range GigabitEthernet0/1-2
Switch(config-if-range)# channel-group 1 mode desirable   ! PAgP

! Sin negociación (ambos extremos deben usar 'on')
Switch(config-if-range)# channel-group 1 mode on
```

### Verificación

```
Switch# show etherchannel summary
! Ejemplo de salida:
!   Group  Port-channel  Protocol    Ports
!   ------+-------------+-----------+-------
!   1      Po1(SU)       LACP        Gi0/1(P) Gi0/2(P)
!   SU = layer2, in use; P = bundled in port-channel

Switch# show etherchannel port-channel
Switch# show interfaces port-channel 1
Switch# show interfaces port-channel 1 trunk
```

---

<svg viewBox="0 0 520 180" width="520" height="180" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;font-family:sans-serif">
  <!-- Switch A -->
  <rect x="30" y="60" width="120" height="60" rx="6" fill="#4a7fa5" opacity="0.3"/>
  <text x="90" y="95" font-size="12" fill="currentColor" text-anchor="middle" font-weight="bold">Switch A</text>
  <!-- Switch B -->
  <rect x="370" y="60" width="120" height="60" rx="6" fill="#4a7fa5" opacity="0.3"/>
  <text x="430" y="95" font-size="12" fill="currentColor" text-anchor="middle" font-weight="bold">Switch B</text>
  <!-- EtherChannel links -->
  <line x1="150" y1="80" x2="370" y2="80" stroke="#c0582a" stroke-width="2"/>
  <line x1="150" y1="90" x2="370" y2="90" stroke="#c0582a" stroke-width="2"/>
  <line x1="150" y1="100" x2="370" y2="100" stroke="#c0582a" stroke-width="2"/>
  <text x="260" y="70" font-size="10" fill="currentColor" text-anchor="middle">EtherChannel (Po1)</text>
  <text x="260" y="115" font-size="9" fill="currentColor" text-anchor="middle" opacity="0.7">3 × GE = 3 Gbps lógico</text>
  <!-- STP sees one link -->
  <text x="260" y="150" font-size="9" fill="#5ba85e" text-anchor="middle">STP ve un único enlace → todos en Forwarding</text>
</svg>

---

## Related Topics

- [[VLANs-y-Conmutacion]] — VLANs y trunking
- [[Guia-Packet-Tracer]] — comandos EtherChannel y STP
