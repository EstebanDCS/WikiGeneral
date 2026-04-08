---
title: ACL — Listas de Control de Acceso
description: "ACLs estándar (1-99), extendidas (100-199) y nombradas. Wildcard masks. Reglas de colocación. Configuración IOS."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-sesion-acl.md, ird-practicas.md, ird-solucion-acl-estandar.md, ird-solucion-acl-extendida.md]
tags: [acl, seguridad, filtrado, ingenieria-redes, universidad]
---

# ACL — Listas de Control de Acceso

## Qué es una ACL

Una **Access Control List** es una lista ordenada de reglas `permit`/`deny` que el router evalúa paquete a paquete para decidir si reenviarlos o descartarlos.

**Usos principales:**
- Filtrado de tráfico entre redes
- Control de acceso remoto (VTY)
- Definir tráfico para NAT o routing de políticas

---

## Procesado de una ACL

1. Las reglas se evalúan en **orden** de arriba a abajo.
2. La **primera regla que coincide** determina la acción (permit o deny). Se para el procesado.
3. Al final de toda ACL hay un `deny any` **implícito** — si ninguna regla coincide, el paquete se descarta.
4. Una ACL vacía **permite todo** (sin reglas no hay deny implícito aplicado hasta que se aplica la ACL).

> [!warning] Recuerda el deny implícito
> Si no hay ningún `permit`, todo el tráfico será descartado. Siempre añadir el `permit` necesario antes del `deny any`.

---

## Wildcard Mask

La **wildcard mask** es el inverso de la máscara de subred. Un bit `0` significa "debe coincidir", un bit `1` significa "no importa".

```
Máscara de subred:  255.255.255.0   → 11111111.11111111.11111111.00000000
Wildcard mask:        0.0.0.255     → 00000000.00000000.00000000.11111111
```

**Palabras clave especiales:**
- `host 192.168.1.1` ≡ `192.168.1.1 0.0.0.0` (un host exacto)
- `any` ≡ `0.0.0.0 255.255.255.255` (cualquier IP)

---

## ACL Estándar (IDs 1–99, 1300–1999)

Filtra **solo por IP de origen**. Coloca **cerca del destino** (para no bloquear tráfico en exceso).

```
! ACL numerada estándar
R1(config)# access-list 10 permit 192.168.1.0 0.0.0.255
R1(config)# access-list 10 permit host 10.0.0.5
R1(config)# access-list 10 deny any

! Aplicar en interfaz
R1(config)# interface GigabitEthernet0/1
R1(config-if)# ip access-group 10 out          ! out = tráfico saliente

! ACL nombrada estándar
R1(config)# ip access-list standard SOLO_LAN
R1(config-std-nacl)# permit 192.168.1.0 0.0.0.255
R1(config-std-nacl)# deny any
```

---

## ACL Extendida (IDs 100–199, 2000–2699)

Filtra por **IP origen, IP destino, protocolo y puerto**. Coloca **cerca del origen** (para descartar antes de usar el ancho de banda).

```
! Sintaxis: access-list <id> {permit|deny} <proto> <src> <wc> <dst> <wc> [eq <port>]

! Permitir solo HTTP y HTTPS desde la LAN hacia cualquier destino
R1(config)# access-list 100 permit tcp 192.168.1.0 0.0.0.255 any eq 80
R1(config)# access-list 100 permit tcp 192.168.1.0 0.0.0.255 any eq 443

! Bloquear ping hacia una red específica
R1(config)# access-list 100 deny icmp any 10.1.0.0 0.0.0.255

! Permitir el resto
R1(config)# access-list 100 permit ip any any

! Aplicar en la interfaz más cercana al origen, en dirección inbound
R1(config)# interface GigabitEthernet0/0
R1(config-if)# ip access-group 100 in
```

**Protocolos comunes:** `ip`, `tcp`, `udp`, `icmp`

**Puertos comunes:**

| Servicio | Puerto |
|----------|--------|
| FTP | 20, 21 |
| SSH | 22 |
| Telnet | 23 |
| SMTP | 25 |
| DNS | 53 |
| HTTP | 80 |
| HTTPS | 443 |

---

## ACL Nombrada

Las ACLs nombradas permiten editar reglas individuales (agregar/borrar por número de secuencia):

```
R1(config)# ip access-list extended FILTRO_TRAFICO
R1(config-ext-nacl)# 10 permit tcp 192.168.1.0 0.0.0.255 any eq 80
R1(config-ext-nacl)# 20 permit tcp 192.168.1.0 0.0.0.255 any eq 443
R1(config-ext-nacl)# 30 deny ip any any

! Borrar regla 10
R1(config-ext-nacl)# no 10

! Insertar nueva regla entre 10 y 20
R1(config-ext-nacl)# 15 permit udp any any eq 53
```

---

## Control de acceso VTY (SSH/Telnet)

```
R1(config)# access-list 20 permit 192.168.1.0 0.0.0.255
R1(config)# line vty 0 4
R1(config-line)# access-class 20 in      ! solo LAN puede acceder por SSH/Telnet
```

---

## Regla mnemotécnica de colocación

```
Estándar → cerca del DESTINO  (solo filtra origen, si está cerca del origen bloquea demasiado)
Extendida → cerca del ORIGEN  (filtra todo, se descarta antes de cruzar la red)
```

---

## Verificación

```
R1# show access-lists                        ! todas las ACLs
R1# show access-lists 100                    ! ACL específica
R1# show ip interface GigabitEthernet0/0     ! ver ACL aplicada en interfaz
R1# clear ip access-list counters            ! resetear contadores
```

---

## Related Topics

- [[NAT-PAT]] — las ACLs definen el tráfico a traducir
- [[Guia-Packet-Tracer]] — ejemplos completos en Packet Tracer
- [[HSRP]] — ACLs para gestión de acceso
