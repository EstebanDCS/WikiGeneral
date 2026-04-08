---
title: VoIP y Enlaces T1/E1
description: "VoIP: digitalización de voz, codecs, protocolos SIP/RTP, calidad (jitter, latencia, packet loss). Multiplexación TDM, T1/E1."
date: 2026-04-08
type: topic
subject: Ingeniería de Redes
sources: [ird-voip-enlacest1e1.md, ird-ingenieria-trafico-voz.md]
tags: [voip, sip, rtp, t1, e1, tdm, voz, ingenieria-redes, universidad]
---

# VoIP y Enlaces T1/E1

## Digitalización de voz

La voz se digitaliza siguiendo el proceso PCM (Pulse Code Modulation):

1. **Muestreo**: 8000 muestras/segundo (frecuencia de Nyquist para voz 0-4 kHz).
2. **Cuantización**: cada muestra → 8 bits (256 niveles).
3. **Codificación**: resultado = `8000 × 8 = 64.000 bps = 64 kbps` por canal de voz.

---

## Multiplexación TDM — T1 y E1

Los sistemas **T1** (EEUU) y **E1** (Europa) multiplexan canales de voz usando **TDM (Time Division Multiplexing)**:

| Parámetro | T1 | E1 |
|-----------|----|----|
| Canales | 24 canales DS0 | 30 canales de voz + 2 señalización |
| Velocidad | 24 × 64 kbps + overhead = **1.544 Mbps** | 32 × 64 kbps = **2.048 Mbps** |
| Trama | 193 bits (24 × 8 + 1 bit framing) | 256 bits (32 × 8) |
| Frecuencia de trama | 8000 tramas/s | 8000 tramas/s |

**Jerarquía PDH (EE.UU.):**
- DS0: 64 kbps (1 canal)
- DS1 (T1): 1.544 Mbps (24 canales)
- DS3 (T3): 44.736 Mbps (672 canales)

---

## VoIP — Voice over IP

VoIP transmite voz digitalizada como paquetes IP en lugar de circuitos dedicados.

**Ventajas:** menor coste, integración con datos, funcionalidades avanzadas.
**Desafíos:** sensible a latencia, jitter y pérdida de paquetes.

### Codecs

| Codec | Bitrate | Calidad MOS | Uso |
|-------|---------|------------|-----|
| G.711 | 64 kbps | 4.4 | PSTN, máxima calidad |
| G.729 | 8 kbps | 3.9 | VoIP estándar |
| G.723.1 | 6.3/5.3 kbps | 3.9/3.7 | Videoconferencia |
| Opus | 6-510 kbps | Variable | WebRTC, moderno |

**MOS (Mean Opinion Score):** puntuación subjetiva de calidad de voz (1=inaceptable, 5=excelente).

### Protocolo SIP (Session Initiation Protocol)

Protocolo de señalización para establecer y terminar sesiones multimedia:

```
Llamante          Servidor SIP         Receptor
   |── INVITE ──────────►|── INVITE ───►|
   |                     |◄── 180 Ring ─|
   |◄──── 180 Ringing ───|              |
   |                     |◄── 200 OK ───|
   |◄──── 200 OK ────────|              |
   |────────────── ACK ──────────────►  |
   |◄═══════ RTP (audio bidireccional) ►|
   |────────── BYE ──────────────────►  |
```

### Protocolo RTP (Real-time Transport Protocol)

Transporta el audio/video en tiempo real:
- Cabecera con timestamp, número de secuencia, SSRC.
- Corre sobre **UDP** (no TCP — la retransmisión llega tarde).
- **RTCP** (RTP Control Protocol): estadísticas de calidad.

---

## Parámetros de calidad VoIP

| Parámetro | Umbral aceptable | Efecto si supera |
|-----------|-----------------|-----------------|
| **Latencia** | < 150 ms (ITU G.114) | Eco perceptible, conversación difícil |
| **Jitter** | < 30 ms | Distorsión, cortes |
| **Packet Loss** | < 1% | Artefactos, cortes |

**Jitter buffer**: buffer en el receptor que absorbe variaciones de retardo. Introduce latencia adicional pero suaviza el jitter.

---

## Related Topics

- [[trafico/Ingenieria-Trafico]] — Erlang y dimensionamiento de tráfico de voz
- [[acceso/Acceso-Banda-Ancha]] — impacto de DSL y cable en la calidad VoIP
- [[ACL]] — priorización de VoIP con QoS/ACLs
