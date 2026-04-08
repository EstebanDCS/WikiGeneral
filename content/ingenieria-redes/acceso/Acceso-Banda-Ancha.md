---
title: Acceso a Banda Ancha
description: "Tecnologías de acceso: DSL (ADSL, VDSL), DOCSIS (HFC cable), fibra óptica (FTTH/GPON), acceso móvil (4G/5G)."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-tecnologias-acceso-banda-ancha.md, ird-redes-cable-docsis.md, ird-acceso-movil.md]
tags: [acceso, banda-ancha, dsl, docsis, fibra, 5g, ingenieria-redes, universidad]
---

# Acceso a Banda Ancha

## DSL — Digital Subscriber Line

DSL transmite datos sobre el **par de cobre telefónico** existente (bucle de abonado) aprovechando frecuencias superiores a las del teléfono (> 4 kHz).

| Variante | Velocidad bajada | Velocidad subida | Distancia máx. |
|----------|-----------------|-----------------|----------------|
| **ADSL** | 8 Mbps | 1 Mbps | ~5 km |
| **ADSL2+** | 24 Mbps | 3.5 Mbps | ~5 km |
| **VDSL** | 52 Mbps | 16 Mbps | ~1.5 km |
| **VDSL2** | 100 Mbps | 100 Mbps | ~500 m |
| **G.fast** | 1 Gbps | 500 Mbps | ~100 m |

**Asimétrico**: la velocidad de bajada (downstream) es mayor que la de subida (upstream), adecuado para el patrón de uso doméstico.

**Splitter**: divide la señal entre teléfono (0-4 kHz) y datos (> 25 kHz) en la roseta.

**DSLAM** (DSL Access Multiplexer): equipo en la central telefónica que agrega múltiples líneas DSL.

---

## DOCSIS — Cable HFC

**HFC (Hybrid Fiber-Coaxial):** red de cable TV que combina fibra óptica en el tramo de distribución y coaxial en el tramo final (hasta los hogares).

**DOCSIS** (Data Over Cable Service Interface Specification) define cómo transmitir datos sobre HFC:

- **Downstream (TV + datos)**: canal de 6 MHz o 8 MHz usando QAM (hasta 256-QAM en DOCSIS 3.0, 4096-QAM en DOCSIS 3.1).
- **Upstream (datos)**: canal de 6.4 MHz usando QPSK o QAM.
- El downstream es **broadcast** (todos los abonados del mismo segmento lo reciben).
- El upstream es compartido y necesita acceso múltiple coordinado.

**Acceso upstream — MAP messages:**
- El **CMTS** (Cable Modem Termination System) en la cabecera envía mensajes **MAP** a los módems de cable.
- Cada MAP asigna **slots de tiempo** para que los módems transmitan en upstream.
- Los módems esperan su slot antes de transmitir → evita colisiones.

**Versiones DOCSIS:**

| Versión | Downstream | Upstream | Año |
|---------|-----------|---------|-----|
| 3.0 | 1 Gbps | 200 Mbps | 2006 |
| 3.1 | 10 Gbps | 1 Gbps | 2013 |
| 4.0 | 10 Gbps | 6 Gbps | 2017 |

---

## Fibra Óptica — FTTH/GPON

**FTTH** (Fiber To The Home): fibra hasta el domicilio, velocidades simétricas de 1 Gbps o más.

**GPON** (Gigabit Passive Optical Network): tecnología más extendida para FTTH.

- **OLT** (Optical Line Terminal): en la central del operador.
- **Splitter óptico**: divide la fibra entre varios usuarios (1:32 o 1:64).
- **ONT/ONU** (Optical Network Terminal/Unit): en el domicilio del abonado.
- **Downstream**: broadcast a todos los ONTs del PON.
- **Upstream**: TDMA — cada ONT transmite en su ventana de tiempo asignada por la OLT.

---

## Acceso Móvil

| Tecnología | Generación | Velocidad típica | Latencia |
|-----------|-----------|-----------------|---------|
| HSPA+ | 3.5G | 21 Mbps / 5.76 Mbps | ~50 ms |
| LTE | 4G | 100 Mbps / 50 Mbps | ~10 ms |
| LTE-A | 4G+ | 1 Gbps | ~5 ms |
| **NR (5G)** | 5G | 10+ Gbps | < 1 ms |

**5G NR (New Radio):**
- Bandas: **sub-6 GHz** (cobertura amplia) y **mmWave** (>24 GHz, muy alta velocidad, corto alcance).
- **Massive MIMO**: cientos de antenas en el mástil para beamforming preciso.
- **Network Slicing**: divisiones virtuales de la red 5G para diferentes aplicaciones (eMBB, URLLC, mMTC).

---

## Comparativa

| Tecnología | Velocidad | Latencia | Mobilidad | Cobertura |
|-----------|-----------|---------|----------|-----------|
| ADSL | Baja | Media | No | Par de cobre |
| VDSL | Media | Media | No | Par de cobre (corto) |
| DOCSIS | Alta | Media | No | Coaxial HFC |
| FTTH | Muy alta | Baja | No | Fibra |
| 4G LTE | Alta | Baja-media | Sí | Celular |
| 5G NR | Muy alta | Muy baja | Sí | Celular |

---

## Related Topics

- [[WLAN]] — WiFi como último tramo inalámbrico en el hogar
- [[trafico/Ingenieria-Trafico]] — gestión de la capacidad en redes de acceso
- [[acceso/MANETs]] — redes ad-hoc móviles
