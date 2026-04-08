---
title: IPv6
description: "IPv6: cabecera de 40 bytes, direcciones de 128 bits, tipos de direcciones, autoconfiguracion y coexistencia con IPv4."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-ipv6.md]
tags: [ipv6, direccionamiento, autoconfiguracion, ingenieria-redes, universidad]
---

# IPv6

## Por qué IPv6

IPv4 tiene solo $2^{32} \approx 4.3 \times 10^9$ direcciones. En 2012, RIPE NCC (Europa) comenzó a repartir el último bloque `/8`. Desde 2019 solo se asignan bloques `/22` o menores. **El espacio IPv4 está agotado.**

IPv6 ofrece $2^{128} \approx 3.4 \times 10^{38}$ direcciones. Suficiente para asignar miles de millones de IPs a cada persona del planeta.

**Mejoras respecto a IPv4:**
- Espacio de direccionamiento masivo (128 vs 32 bits)
- Cabecera más simple y eficiente (40 bytes fijos, sin checksum)
- Autoconfiguraci&oacute;n nativa (SLAAC — no necesita DHCP)
- IPsec nativo obligatorio
- No hay NAT (cada dispositivo tiene IP global)
- Soporte nativo para movilidad (Mobile IPv6)
- Multicast mejorado (no hay broadcast)

---

## Cabecera IPv6

La cabecera tiene **longitud fija de 40 bytes** (vs IPv4 que es variable):

| Campo | Bits | Descripción |
|-------|------|-------------|
| Version | 4 | Siempre 6 |
| Traffic Class | 8 | Equivalente a ToS de IPv4 (QoS) |
| Flow Label | 20 | Identifica flujos (útil con IPsec) |
| Payload Length | 16 | Bytes después de la cabecera IPv6 |
| Next Header | 8 | Tipo del siguiente cabecera (TCP=6, UDP=17, ICMPv6=58...) |
| Hop Limit | 8 | Equivalente al TTL de IPv4 |
| Source Address | 128 | Dirección IPv6 origen |
| Destination Address | 128 | Dirección IPv6 destino |

> [!tip] Eliminaciones respecto a IPv4
> IPv6 elimina: IHL, ToS, Identification, Flags, Fragment Offset, Checksum, y Options. Esto simplifica el procesado en routers → mayor velocidad.

**Cabeceras de extensión:** en vez de Options, IPv6 usa cabeceras de extensión encadenadas (daisy chaining): hop-by-hop, routing, fragmentation, autenticación (AH), cifrado (ESP).

---

## Direccionamiento IPv6

### Formato

128 bits escritos como **8 grupos de 4 dígitos hexadecimales** separados por `:`:

```
2001:0DB8:0000:CD31:0123:4567:89AB:CDEF
```

**Reglas de compresión:**
1. Ceros iniciales en cada grupo se pueden omitir: `0DB8` → `DB8`
2. Un único grupo de ceros consecutivos se puede reemplazar por `::` (solo una vez):
   - `2001:0DB8:0000:0000:0000:0000:0000:0001` → `2001:DB8::1`

### Tipos de direcciones

| Tipo | Prefijo | Descripción |
|------|---------|-------------|
| **Unicast global** | `2000::/3` | Equivalente a IP pública IPv4. Ruteables en Internet. |
| **Link-local** | `FE80::/10` | Solo válida en el enlace local. Generada automáticamente. Obligatoria en toda interfaz IPv6. |
| **Loopback** | `::1/128` | Equivalente a 127.0.0.1 |
| **Multicast** | `FF00::/8` | Sustituye al broadcast de IPv4. Grupos de destinos. |
| **Anycast** | — | Misma dirección en varios nodos; el paquete llega al más cercano. |
| **Dirección no especificada** | `::/128` | Equivalente a 0.0.0.0 |

> [!warning] No hay broadcast en IPv6
> El broadcast se reemplaza por multicast. Por ejemplo, `FF02::1` equivale a "todos los nodos del enlace".

### Generación de la dirección Link-Local (EUI-64)

La dirección link-local se genera automáticamente a partir de la MAC (EUI-48):

$$\text{MAC: } 00{:}19{:}0E{:}0A{:}45{:}63 \quad \Rightarrow \quad \text{EUI-64: } 02{:}19{:}0E{:}\text{FF:FE:}0A{:}45{:}63$$

Se inserta `FF:FE` en el medio y se invierte el bit U/L (bit 7 del primer byte).

Dirección link-local resultante: `FE80::0219:0EFF:FE0A:4563/64`

---

## Autoconfiguraci&oacute;n (SLAAC)

**Stateless Address Autoconfiguration (SLAAC):** un dispositivo puede configurarse solo, sin DHCP:

1. Genera la dirección link-local (FE80::/10 + EUI-64)
2. Realiza **Duplicate Address Detection (DAD)** — verifica que nadie más usa esa dirección
3. Escucha los **Router Advertisement (RA)** del router → obtiene el prefijo de red
4. Construye su dirección global: prefijo (/64) + EUI-64

**DHCPv6** también existe para configuración más controlada (stateful o stateless).

---

## Coexistencia IPv6/IPv4

| Mecanismo | Descripción |
|-----------|-------------|
| **Dual-stack** | El dispositivo tiene IPv4 e IPv6 simultáneamente |
| **Tunneling** | Paquetes IPv6 encapsulados dentro de IPv4 (6to4, 6in4, Teredo) |
| **Traducción (NAT64)** | Comunicación entre nodos solo-IPv4 y solo-IPv6 |

---

## Configuración en Cisco IOS

```
! Habilitar IPv6 en el router
Router(config)# ipv6 unicast-routing

! Configurar dirección IPv6 en interfaz
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ipv6 address 2001:DB8:1:1::1/64
Router(config-if)# no shutdown

! Verificar
Router# show ipv6 interface brief
Router# show ipv6 route
Router# ping ipv6 2001:DB8:1:2::1
```

---

## Related Topics

- [[Arquitectura-Internet]] — el problema del agotamiento de IPv4
- [[DHCP]] — DHCPv6 para configuración dinámica
- [[Sistemas-Autonomos-y-Routing]] — OSPFv3 y RIPng para IPv6
- [[Guia-Packet-Tracer]] — comandos IOS para IPv6
