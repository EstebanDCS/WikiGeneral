---
title: WLAN — IEEE 802.11
description: "Redes inalámbricas 802.11: estándares (n/ac/ax/be), capas MAC y PHY, CSMA/CA, modos infraestructura y ad-hoc, seguridad WPA2/WPA3."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-wlan-80211ac.md, ird-wlan-v3-notas.md]
tags: [wlan, wifi, 802.11, inalambrico, ingenieria-redes, universidad]
---

# WLAN — IEEE 802.11

## Estándares IEEE 802.11

| Estándar | Nombre | Banda | MIMO | Velocidad máx. | Año |
|----------|--------|-------|------|----------------|-----|
| 802.11b | — | 2.4 GHz | No | 11 Mbps | 1999 |
| 802.11g | — | 2.4 GHz | No | 54 Mbps | 2003 |
| 802.11n | **WiFi 4** | 2.4/5 GHz | Sí (4×4) | 600 Mbps | 2009 |
| 802.11ac | **WiFi 5** | 5 GHz | Sí (8×8) | 6.9 Gbps | 2013 |
| 802.11ax | **WiFi 6/6E** | 2.4/5/6 GHz | Sí (8×8) | 9.6 Gbps | 2019 |
| 802.11be | **WiFi 7** | 2.4/5/6 GHz | Sí (16×16) | 46 Gbps | 2024 |

---

## Capas 802.11

### Capa física (PHY)

Transmite bits mediante:
- **OFDM** (Orthogonal Frequency Division Multiplexing): divide el canal en subportadoras ortogonales. Usado desde 802.11a/g.
- **OFDMA** (802.11ax): múltiples usuarios comparten subcanales simultáneamente.
- **MIMO** (Multiple Input Multiple Output): múltiples antenas TX/RX para aumentar capacidad y fiabilidad.
- **MU-MIMO** (Multi-User MIMO): sirve a varios clientes simultáneamente.

**Canales:**
- **2.4 GHz**: 11 canales solapados, solo 3 no se solapan (1, 6, 11).
- **5 GHz**: más canales, sin solapamiento, pero menor alcance.
- **6 GHz** (WiFi 6E): banda nueva, sin interferencias de dispositivos legacy.

### Capa de acceso al medio (MAC)

802.11 usa **CSMA/CA** (Carrier Sense Multiple Access with Collision Avoidance):

1. **Escuchar** el canal antes de transmitir (carrier sense).
2. Si libre → esperar un tiempo aleatorio (backoff) → transmitir.
3. Si ocupado → esperar hasta que quede libre + backoff.
4. En lugar de detectar colisiones (como Ethernet), las **evita** con el mecanismo de backoff.

**ACK obligatorio**: todo frame unicast requiere ACK del receptor. Si no llega → retransmisión.

**RTS/CTS** (Request to Send / Clear to Send): mecanismo opcional para redes congestionadas con el **problema del nodo oculto**.

---

## Modos de funcionamiento

### Modo infraestructura

```
[Cliente] ←→ [AP (Access Point)] ←→ [Red cableada]
```

- El **AP** es el punto central de la WLAN.
- **BSS** (Basic Service Set): un AP + sus clientes.
- **SSID** (Service Set Identifier): nombre de la red WiFi.
- **ESS** (Extended Service Set): múltiples APs con el mismo SSID → roaming.
- **BSSID**: dirección MAC del AP.

### Modo ad-hoc (IBSS)

Comunicación directa entre clientes sin AP. Poco usado en práctica.

---

## Proceso de asociación

1. **Escaneo**: el cliente busca APs (pasivo: espera Beacons; activo: envía Probe Request).
2. **Autenticación**: intercambio de credenciales.
3. **Asociación**: el cliente se asocia al AP elegido.
4. **DHCP**: el cliente obtiene IP.

---

## Seguridad

| Protocolo | Cifrado | Estado |
|-----------|---------|--------|
| WEP | RC4 (40-104 bits) | **Obsoleto** — roto en minutos |
| WPA | TKIP | **Obsoleto** |
| **WPA2** | AES-CCMP (128 bits) | Estándar actual |
| **WPA3** | SAE + AES-GCMP (192 bits) | Recomendado |

**Modos de autenticación:**
- **Personal (PSK)**: contraseña compartida. Para uso doméstico.
- **Enterprise (802.1X)**: servidor RADIUS. Para empresas.

---

## Parámetros de rendimiento

**Throughput real** ≈ 50-60% del teórico (overhead de protocolos, ACKs, CSMA/CA).

**Factores que afectan al rendimiento:**
- Distancia al AP y obstáculos
- Interferencias de otras redes 802.11 y dispositivos (microondas, bluetooth)
- Número de clientes en el mismo BSS
- Versión de estándar (client y AP deben coincidir para el máximo)

---

## Related Topics

- [[acceso/Acceso-Banda-Ancha]] — DSL y cable como backhaul de WiFi
- [[VLANs-y-Conmutacion]] — APs suelen conectarse a switches con VLANs por SSID
