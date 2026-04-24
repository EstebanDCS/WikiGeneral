---
title: Wiki Overview
description: "Síntesis global de la wiki — cobertura, estructura, estado y conexiones entre asignaturas."
type: overview
date: 2026-04-24
tags: [overview, universidad, meta]
---

# Wiki Overview

Base de conocimiento personal construida con el patrón LLM Wiki: fuentes inmutables en `raw/`, conocimiento compilado y mantenido por Claude Code en `content/`. El wiki crece con cada fuente nueva; el conocimiento se acumula en vez de redescubrirse.

---

## Estado actual

| Asignatura | Temas | Fuentes | Estado |
|-----------|-------|---------|--------|
| [[sistemas-inteligentes/index\|Sistemas Inteligentes]] | 15 | 20 | ✅ Completo |
| [[computacion-numerica/index\|Computación Numérica]] | 11 | 10 | ✅ Completo |
| [[ingenieria-redes/index\|Ingeniería de Redes]] | 17 | 24 | ✅ Completo |
| [[sistemas-informacion/index\|Sistemas de Información]] | 6 | 11 | ✅ Completo |
| [[google-cloud/index\|Google Cloud Platform]] | 6 | 8 labs | ✅ Completo |

**Total:** ~55 temas · ~73 fuentes · ~105 páginas markdown

---

## Estructura del wiki

```
content/
├── index.md              ← home navegable (por asignatura)
├── overview.md           ← este archivo
├── log.md                ← registro cronológico de actividad
│
├── sistemas-inteligentes/
│   ├── fundamentos/      → 7 temas (IA, KDD, preprocesado, visualización, reducción)
│   ├── algoritmos/       → 5 temas (regresión, validación, clasificación, redes, CNN)
│   ├── conceptos-clave/  → 3 temas (overfitting, desbalanceo, herramientas Python)
│   ├── fuentes/teoria/   → 11 documentos (PDFs + ejercicios)
│   ├── fuentes/practicas/→ 9 notebooks (P2.1–P4.3, dataset F1 Monaco 2023)
│   └── personas/         → John McCarthy, Frank Rosenblatt
│
├── computacion-numerica/
│   ├── fundamentos/      → 1 tema (aritmética finita, IEEE-754, error)
│   ├── ecuaciones-no-lineales/ → 4 temas (bisección, punto fijo, Newton-Raphson, algebraicas)
│   ├── sistemas-lineales/→ 3 temas (directos, iterativos, no lineales)
│   ├── interpolacion/    → 2 temas (interpolación polinomial, ajuste mínimos cuadrados)
│   ├── integracion/      → 1 tema (cuadratura, trapecio, Simpson, Gaussiana)
│   └── fuentes/          → 5 PDFs de teoría + 5 hojas de problemas
│
├── ingenieria-redes/
│   ├── fundamentos/      → 2 temas (arquitectura Internet, subredes y CIDR)
│   ├── enrutamiento/     → 5 temas (AS y routing, OSPF, BGP, VLANs, MPLS)
│   ├── servicios/        → 6 temas (IPv6, DHCP, NAT/PAT, ACL, HSRP, EtherChannel/STP)
│   ├── acceso/           → 3 temas (WLAN 802.11, banda ancha, MANETs)
│   ├── trafico/          → 2 temas (ingeniería de tráfico, VoIP / T1-E1)
│   ├── practicas/        → Guía Cisco IOS / Packet Tracer
│   └── fuentes/          → 14 PDFs teoría + 10 PDFs prácticas
│
├── sistemas-informacion/
│   ├── metodologia/      → Scrum y métodos ágiles
│   ├── requisitos/       → 2 temas (requisitos software, historias de usuario)
│   ├── calidad/          → 2 temas (pruebas software, estándares ISO 29119)
│   ├── practicas/        → Git y Pull Requests
│   └── fuentes/          → 8 PDFs teoría + 3 PDFs prácticas
│
└── google-cloud/
    ├── almacenamiento/   → Cloud Storage (GSP074)
    ├── iam/              → IAM Roles Personalizados (GSP190)
    ├── funciones/        → Cloud Run Functions (GSP080 + GSP081)
    ├── monitoreo/        → Cloud Monitoring + Métricas en Logs (GSP089–GSP091)
    └── app-engine/       → App Engine Java (GSP068)
```

---

## Síntesis por asignatura

### Sistemas Inteligentes

El dominio central del wiki. Cubre el **pipeline completo de ML**: desde la recogida y limpieza de datos (KDD, preprocesado) hasta el despliegue de redes neuronales convolucionales. El **dataset F1 Monaco 2023** actúa de hilo conductor en todas las prácticas P2.2–P4.2; MNIST en P4.3.

Algoritmos cubiertos: regresión (lineal, polinómica, SVR, KNN, Bayesiano), clasificación (logística, SVM, árboles de decisión, KNN, Naive Bayes), validación cruzada K-fold, redes neuronales multicapa y CNNs con Keras/TensorFlow.

Conceptos transversales clave: [[sistemas-inteligentes/conceptos-clave/Overfitting-y-Underfitting|overfitting y underfitting]] (regularización, dropout, early stopping), [[sistemas-inteligentes/conceptos-clave/Desbalanceo-de-Clases|desbalanceo de clases]] (SMOTE, class_weight, F1 macro), [[sistemas-inteligentes/conceptos-clave/Herramientas-Python-ML|stack Python ML]] (Pandas, sklearn, Keras, Matplotlib).

Figuras históricas: [[sistemas-inteligentes/personas/John-McCarthy|John McCarthy]] (término "IA", 1956) y [[sistemas-inteligentes/personas/Frank-Rosenblatt|Frank Rosenblatt]] (Perceptrón, 1957).

**Pendiente:** contenido de T7 Anomalías y Representación Vectorial (PPTXs).

---

### Computación Numérica

Asignatura de Prof. Alejandro Buendía (U. Oviedo). Tema central: **cómo resolver matemáticas sin solución analítica exacta**, controlando el error en cada paso.

Estructura en 5 bloques con complejidad creciente: error → ecuaciones no lineales → sistemas lineales → interpolación → integración. El método estrella de cada bloque: [[computacion-numerica/ecuaciones-no-lineales/Newton-Raphson|Newton-Raphson]] (orden 2, ecuaciones), [[computacion-numerica/sistemas-lineales/Metodos-Directos-Sistemas|Gauss con pivoteo]] (sistemas), [[computacion-numerica/interpolacion/Interpolacion-Polinomial|splines cúbicos]] (interpolación), [[computacion-numerica/integracion/Integracion-Numerica|cuadratura Gaussiana]] (integración).

**Conexión con SI:** los errores de redondeo y la aritmética finita afectan directamente a la convergencia de algoritmos de ML (ej. gradiente descendente).

---

### Ingeniería de Redes

Asignatura de 2º curso (Profs. Roberto García, Ángel Neira, Carlos González — U. Oviedo). Cobertura muy amplia: **arquitectura de Internet de extremo a extremo**, desde la capa física hasta el enrutamiento inter-dominio.

Protocolos y tecnologías clave: [[ingenieria-redes/enrutamiento/OSPF|OSPF]] (estado de enlace, Dijkstra, coste=10⁸/BW dentro de un AS), [[ingenieria-redes/enrutamiento/BGP|BGP]] (conecta los AS de Internet, política sobre métricas), [[ingenieria-redes/enrutamiento/VLANs-y-Conmutacion|VLANs + trunking + inter-VLAN routing]] (núcleo de los labs de switching), [[ingenieria-redes/enrutamiento/MPLS|MPLS]] (conmutación de etiquetas para ingeniería de tráfico y VPNs), [[ingenieria-redes/servicios/ACL|ACL]] (extendidas cerca del origen, estándar cerca del destino).

**Prácticas:** [[ingenieria-redes/practicas/Guia-Packet-Tracer|Guía Cisco IOS / Packet Tracer]] cubre todos los comandos necesarios para los laboratorios.

**Conexión con GCP:** IPv6, subredes CIDR y firewalls (ACL) son fundamentales para configurar VPCs en Google Cloud.

---

### Sistemas de Información

Asignatura de 3º curso (Profs. Claudio de la Riva, Javier Tuya, Raquel Blanco, Rubén Zurita — U. Oviedo). Gira en torno a un **proyecto Scrum real** (app de carreras populares) con 3 sprints.

Pipeline de calidad: pliego de requisitos → [[sistemas-informacion/requisitos/Historias-de-Usuario|historias de usuario con criterios de aceptación]] → [[sistemas-informacion/calidad/Pruebas-Software|casos de prueba con clases de equivalencia]] → automatización JUnit. Los [[sistemas-informacion/practicas/Git-y-Pull-Requests|Pull Requests en GitHub]] actúan como revisión estática (prueba estática) + SCM. Los [[sistemas-informacion/calidad/Estandares-ISO|estándares ISO 29119]] formalizan el proceso de pruebas.

**Conexión con IRD:** Git y los flujos de trabajo de colaboración documentados aquí son directamente aplicables a los proyectos de redes.

---

### Google Cloud Platform

Laboratorios del **Google Cloud Skills Boost Challenge** (8 labs completados). Cubre las capas de abstracción serverless de GCP: Cloud Run Functions (event-driven + HTTPS) → Cloud Run → App Engine (entorno estándar Java).

Conceptos transversales: IAM con permisos `<servicio>.<recurso>.<verbo>` y ciclo de vida de roles personalizados (ALPHA→GA→DISABLED); Cloud Monitoring con Metrics Scope multi-proyecto; métricas basadas en logs para observabilidad sin instrumentar código.

**Conexión con IRD:** Los conceptos de subredes, firewalls (equivalente a ACLs) y arquitectura de red son directamente aplicables al diseño de VPCs en GCP.

---

## Conexiones entre asignaturas

```
Sistemas Inteligentes ←→ Computación Numérica
  Gradiente descendente usa aritmética finita del Tema 1 CN
  Convergencia de Newton-Raphson ≡ convergencia del optimizador Adam

Ingeniería de Redes ←→ Google Cloud
  CIDR/subredes → VPCs en GCP
  ACLs → Firewall rules en GCP
  IPv6 → soporte nativo en GCP

Sistemas de Información ←→ Todos
  Git/GitHub → SCM en todos los proyectos
  Pruebas de software → aplicable a pipelines ML y labs GCP
```

---

## Preguntas abiertas y gaps

- **T7 Anomalías (SI):** el PPTX no fue ingestado aún — detección de anomalías es un tema de ML no supervisado importante.
- **Representación Vectorial (SI):** otro PPTX pendiente — relevante para NLP y embeddings.
- **Exámenes pasados:** ¿hay exámenes de años anteriores para CN, IRD o SI?
- **Prácticas IRD:** la guía Packet Tracer existe pero las prácticas individuales (lab PDFs) no tienen páginas resumen todavía.
- **Fuentes CN:** los PDFs t1–t5 y las hojas de problemas están referenciados pero no tienen página resumen propia.
- **Tema pendiente SI:** no hay página para el proyecto completo Scrum con los 3 sprints — podría ser un análisis valioso.
