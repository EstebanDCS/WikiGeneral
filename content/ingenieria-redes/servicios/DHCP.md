---
title: DHCP — Dynamic Host Configuration Protocol
description: "DHCP: asignación dinámica de IPs. Proceso DORA, servidor en router Cisco, relay agent, DHCPv6 y SLAAC."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-practica-dhcp.md, ird-interconexion-redes.md]
tags: [dhcp, direccionamiento, dinamico, ipv6, ingenieria-redes, universidad]
---

# DHCP — Dynamic Host Configuration Protocol

## Qué es DHCP

Protocolo de aplicación (UDP puertos **67** servidor / **68** cliente) que permite a los hosts obtener configuración de red automáticamente:

- Dirección IP
- Máscara de subred
- Puerta de enlace predeterminada (default gateway)
- Servidor DNS

Sin DHCP, cada dispositivo necesitaría configuración IP manual.

---

## Proceso DORA

La asignación de dirección sigue cuatro mensajes broadcast:

| Mensaje | Origen | Destino | Contenido |
|---------|--------|---------|-----------|
| **D**iscover | Cliente (0.0.0.0) | 255.255.255.255 | "¿Hay servidores DHCP?" |
| **O**ffer | Servidor | 255.255.255.255 | Propuesta de IP + parámetros |
| **R**equest | Cliente | 255.255.255.255 | "Acepto la IP ofrecida" |
| **A**cknowledge | Servidor | 255.255.255.255 | Confirmación + lease time |

```
Cliente          Servidor
  |── DISCOVER ──►|
  |◄── OFFER ─────|
  |── REQUEST ────►|
  |◄── ACK ────────|
```

Cuando el **lease** expira, el cliente lo renueva con mensajes **Request/ACK** unicast.

---

## Configuración en Cisco IOS (servidor DHCP en router)

```
! Excluir IPs de la asignación dinámica (routers, servidores, etc.)
R1(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10

! Definir el pool DHCP
R1(config)# ip dhcp pool LAN_POOL
R1(dhcp-config)# network 192.168.1.0 255.255.255.0
R1(dhcp-config)# default-router 192.168.1.1
R1(dhcp-config)# dns-server 8.8.8.8 8.8.4.4
R1(dhcp-config)# domain-name empresa.local
R1(dhcp-config)# lease 7                   ! días (defecto: 1 día)

! Verificación
R1# show ip dhcp pool
R1# show ip dhcp binding         ! IPs asignadas y sus MACs
R1# show ip dhcp conflict        ! conflictos detectados
R1# show ip dhcp server statistics
```

---

## DHCP Relay Agent

Cuando el servidor DHCP está en una red diferente al cliente, el router más cercano puede reenviar los broadcasts como unicast al servidor:

```
! En la interfaz del router más cercana a los clientes
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip helper-address 10.0.0.100   ! IP del servidor DHCP remoto
```

El `ip helper-address` convierte el broadcast DHCP en unicast hacia el servidor, y reenvía la respuesta al cliente.

---

## DHCPv6

Para IPv6 existen dos modos:

| Modo | Descripción |
|------|-------------|
| **SLAAC** (Stateless) | El dispositivo genera su IP con el prefijo del RA + EUI-64. Sin DHCP. |
| **DHCPv6 Stateless** | SLAAC para la IP, DHCPv6 para opciones (DNS, dominio). |
| **DHCPv6 Stateful** | DHCPv6 asigna todo (IP + opciones), como DHCPv4. |

```
! Configurar servidor DHCPv6 stateless en router
R1(config)# ipv6 dhcp pool LAN_IPV6
R1(config-dhcpv6)# dns-server 2001:4860:4860::8888
R1(config-dhcpv6)# domain-name empresa.local

R1(config)# interface GigabitEthernet0/0
R1(config-if)# ipv6 dhcp server LAN_IPV6
R1(config-if)# ipv6 nd other-config-flag   ! indica a clientes usar DHCPv6 para opciones
```

---

## Related Topics

- [[IPv6]] — SLAAC y autoconfiguraicón IPv6
- [[Guia-Packet-Tracer]] — comandos DHCP en IOS
- [[NAT-PAT]] — cómo NAT afecta al DHCP en redes con IPs privadas
