# Activity Log

## [2026-04-24] output | Tema 6 — 9 ejercicios de optimización resueltos
- Source: ejercicios (3).pdf — Dept. Matemáticas, Computación Numérica
- Página creada: [[computacion-numerica/Tema6-Ejercicios-Resueltos]]
- Contenido: Prb1-3 sección áurea (con tabla iteración por iteración), Prb4-7 máxima pendiente (con búsqueda lineal explícita), Prb8-9 símplex tabular completo
- Incluye: soluciones analíticas de contraste, tabla de errores frecuentes, estrategias por tipo

## [2026-04-24] output | Guías Examen CN — 3 páginas
- Sources: Marzo26_1erParcial.pdf (A+B con soluciones), Tema5-Integracion.pdf, Tema6-Optimizacion.pdf, Prb_IntegraNumerica.pdf, Sesiones PL 1–8
- Páginas creadas: [[computacion-numerica/Examen-Parcial-1-Guia]], [[computacion-numerica/Examen-Parcial-2-Guia]], [[computacion-numerica/Examen-PL-Maxima-Guia]]
- Contenido Parcial 1: aritmética finita con errores reales (13%, 59%), bisección+Bolzano+NR, Cholesky, LU, Jacobi/Gauss-Seidel con radio espectral
- Contenido Parcial 2: Lagrange/Newton/Chebyshev/splines, reglas Newton-Cotes simples y compuestas (O(h²)/O(h⁴)), Gauss-Legendre, sección áurea, gradiente conjugado, símplex
- Contenido PL: ejercicios garantizados (bisec Ex1, M(n) sistemas Ex8, Lagrange Ex15), patrones de todas las sesiones, tabla de errores frecuentes
- Acceso rápido añadido en index.md

## [2026-04-24] analysis | Guía Examen Práctico SI
- Source: Plantilla_PL4.ipynb (examen real con loan_data.csv), P3.1, P3.2, P4.1 (notebooks actualizados)
- Página creada: [[sistemas-inteligentes/Examen-Practico-Guia]]
- Contenido: receta completa de las 3 partes del examen — preprocesado, 5 modelos clásicos, 3 redes neuronales
- Incluye: código copiable, tabla de errores frecuentes, referencia de imports, métricas por tipo de problema
- Dataset del examen identificado: loan_data.csv (predicción de impago de préstamo → clasificación binaria)
- Acceso rápido añadido en index.md

## [2026-04-24] overview | Síntesis global del wiki
- Acción: reescritura completa de `overview.md` — ahora cubre las 5 asignaturas con síntesis por dominio, tabla de estado, árbol de estructura y sección de conexiones entre asignaturas
- Páginas actualizadas: `overview.md`
- Añadida sección "Preguntas abiertas y gaps" con 6 elementos pendientes identificados

## [2026-04-24] lint | Health check completo
- Páginas analizadas: 105 archivos .md, 5 asignaturas
- **Problemas encontrados y corregidos:**
  - `Overfitting y Underfitting.md` (raíz) — archivo de 1 línea vacío referenciado por 9 páginas; convertido en stub de redirección hacia `sistemas-inteligentes/conceptos-clave/Overfitting-y-Underfitting`
  - `ingenieria-redes/index.md` línea 18 — link roto `[[fundamentos/Modelos-TCP-IP]]` (archivo inexistente); redirigido al anchor `#modelo-tcpip-y-capas` dentro de `Arquitectura-Internet.md` donde ya está el contenido
- **Problemas identificados, no corregidos (requieren fuentes o decisión):**
  - ~25 wikilinks con acentos/ñ en páginas de SI teoría (`[[Regresión]]`, `[[Clasificación]]`, etc.) vs filenames con hyphens — Quartz puede resolverlos via slug normalization, monitorizar en build
  - `[[Aprendizaje por Refuerzo]]`, `[[Clustering]]`, `[[KNN]]`, `[[SVM]]`, `[[Naive Bayes]]` — temas referenciados sin página propia en SI; candidatos a crear en próximo ingest
  - `computacion-numerica/fuentes/index.md` — links a `[[t1-aritmetica-finita]]`…`[[t5-integracion]]` y `[[prb-compnum-0]]`…`[[prb-integracion]]` apuntan a páginas resumen de PDFs no creadas aún
- **Páginas candidatas a crear (gaps identificados):**
  - `sistemas-inteligentes/temas/Aprendizaje-por-Refuerzo.md` — referenciado 3 veces, sin página
  - `sistemas-inteligentes/temas/T7-Anomalias.md` — PPTX pendiente de ingest
  - `sistemas-inteligentes/temas/Representacion-Vectorial.md` — PPTX pendiente de ingest
  - Páginas resumen para PDFs CN (t1–t5, prb-compnum-*)
- **Estado general:** contenidos completos y bien estructurados; la mayoría de los "links rotos" detectados por análisis estático son en realidad correctos en Quartz (slug normalization)

## [2026-04-09] ingest | Google Cloud Skills Boost — 8 laboratorios
- Sources: 8 transcripts de laboratorios (GSP074, GSP190, GSP080, GSP081, GSP089, GSP090, GSP091, GSP068) — Google Cloud Skills Boost Challenge
- Contenido no universitario — subject: Google Cloud
- Estructura creada: google-cloud/{almacenamiento, iam, funciones, monitoreo, app-engine}
- Topic pages creadas: Cloud-Storage, IAM-Roles-Personalizados, Cloud-Run-Functions, Cloud-Monitoring, Metricas-Basadas-en-Logs, App-Engine-Java
- GSP080+GSP081 consolidados en una página (CLI vs Consola del mismo servicio); GSP089+GSP090 en Cloud-Monitoring
- Key takeaways: GCP tiene tres capas de serverless (Functions→Run→App Engine) con distintos trade-offs. IAM usa permisos `<servicio>.<recurso>.<verbo>` con roles personalizados con ciclo de vida ALPHA→GA→DISABLED. Cloud Monitoring puede agregar métricas multi-proyecto con Metrics Scope. Las métricas basadas en logs evitan instrumentar código.

## [2026-04-09] ingest | Sistemas de Información — Asignatura completa
- Sources: 8 PDFs de teoría + 3 PDFs de prácticas (Profs. Claudio de la Riva, Javier Tuya, Raquel Blanco, Rubén Zurita — Univ. Oviedo)
- Archivos organizados en: raw/pdfs/sistemas-informacion/ y raw/pdfs/sistemas-informacion/practicas/
- Estructura creada: sistemas-informacion/{metodologia, requisitos, calidad, practicas, fuentes}
- Topic pages creadas: Scrum-Agile, Requisitos-Software, Historias-de-Usuario, Pruebas-Software, Estandares-ISO, Git-y-Pull-Requests
- Key takeaways: SI gira en torno a un proyecto Scrum real (app carreras populares) con 3 sprints. Los requisitos de usuario (pliego) → HUs con criterios de aceptación → casos de prueba con clases de equivalencia → automatización JUnit. Los Pull Requests en GitHub actúan como revisión estática (prueba estática) + SCM.

## [2026-04-08] ingest | Ingeniería de Redes — Asignatura completa
- Sources: 14 PDFs de teoría + 10+ PDFs de prácticas (Profs. Roberto García, Ángel Neira, Carlos González — Univ. Oviedo)
- Archivos organizados en: raw/pdfs/ingenieria-redes/ y raw/pdfs/ingenieria-redes/practicas/
- Estructura creada: ingenieria-redes/{fundamentos, enrutamiento, servicios, acceso, trafico, practicas, fuentes}
- Topic pages creadas: Arquitectura-Internet, Subredes-y-CIDR, Sistemas-Autonomos-y-Routing, OSPF, BGP, VLANs-y-Conmutacion, MPLS, IPv6, DHCP, NAT-PAT, ACL, HSRP, EtherChannel-STP, WLAN, Acceso-Banda-Ancha, MANETs, Ingenieria-Trafico, VoIP
- Output creado: Guia-Packet-Tracer — referencia completa Cisco IOS para todas las prácticas
- Key takeaways: OSPF es el IGP estrella (estado de enlace, Dijkstra, coste=10^8/BW). BGP conecta los AS en Internet. VLANs + trunking + inter-VLAN routing son el núcleo de las prácticas de switching. ACL extendidas cerca del origen, estándar cerca del destino.

## [2026-04-08] ingest | Computación Numérica — Temas 1–5 y hojas de problemas
- Sources: 5 PDFs de teoría + 5 PDFs de problemas (Prof. Alejandro Buendía, Univ. Oviedo)
- Archivos movidos a: raw/pdfs/computacion-numerica/ y raw/pdfs/computacion-numerica/problemas/
- Estructura creada: computacion-numerica/{fundamentos, ecuaciones-no-lineales, sistemas-lineales, interpolacion, integracion, fuentes}
- Topic pages creadas: Aritmetica-Finita-y-Error, Biseccion-y-Regula-Falsi, Punto-Fijo, Newton-Raphson, Ecuaciones-Algebraicas, Metodos-Directos-Sistemas, Metodos-Iterativos-Sistemas, Sistemas-No-Lineales, Interpolacion-Polinomial, Ajuste-de-Datos, Integracion-Numerica
- Key takeaways: La asignatura cubre los 5 grandes bloques de métodos numéricos. Newton-Raphson (orden 2) es el método estrella para ecuaciones no lineales. Gauss con pivoteo para sistemas lineales. Cuadratura Gaussiana para integración eficiente.

## [2026-04-08] reorganize | Estructura por carpetas + optimización Quartz
- Acción: reorganizados todos los archivos de wiki/ en subcarpetas por asignatura y sección
- Nueva estructura: sistemas-inteligentes/{fundamentos, algoritmos, conceptos-clave, fuentes/{teoria,practicas}, personas}
- Creadas páginas índice (index.md) para cada carpeta
- Frontmatter actualizado en todos los archivos: añadidos `description` y `date` (compatibilidad Quartz)
- wiki/index.md rediseñado como home page navegable

## [2026-04-08] ingest | Prácticas y archivos finales de Sistemas Inteligentes
- Sources: Ejercicio métricas (pdf), P2.1–P4.3 (9 notebooks), T7 Anomalías (pptx), Representación Vectorial (pptx)
- Archivos movidos a: raw/pdfs/sistemas-inteligentes/, raw/notes/sistemas-inteligentes/, raw/presentations/sistemas-inteligentes/
- Pages created: [[ejercicio-metricas]], [[p2.1-introduccion-entorno]], [[p2.2-preprocesado-practica]], [[p2.3-visualizacion-practica]], [[p3.1-clasificacion-practica]], [[p3.1-desbalanceo-practica]], [[p3.2-regresion-practica]], [[p4.1-redes-regresion-practica]], [[p4.2-redes-clasificacion-practica]], [[p4.3-cnn-practica]], [[t7-anomalias]], [[vectorial-representation]]
- Topics created: [[Desbalanceo-de-Clases]], [[Redes-Convolucionales]], [[Herramientas-Python-ML]]
- Key takeaways: Las prácticas usan el dataset F1 Monaco 2023. Los PPTXs no son procesables automáticamente. El ejercicio de métricas ilustra perfectamente el peligro del accuracy con clases desbalanceadas.
- Pendiente: ingestar T7_Anomalias.pptx y Vectorial_Representation.pptx manualmente.

## [2026-04-08] ingest | Métodos de Regresión — Tema 2.1
- Source: raw/pdfs/sistemas-inteligentes/2.1_Regresión.pdf
- Pages created/updated: [[2.1-regresion]], [[Regresion]]
- Key takeaways: Baselines, regresión lineal/polinómica, SVR (kernels), árboles, KNN, Bayesianos. Métricas: R², MAE, RMSE.

## [2026-04-08] ingest | Técnicas de Validación — Tema 2.2
- Source: raw/pdfs/sistemas-inteligentes/2.2_Validación.pdf
- Pages created/updated: [[2.2-validacion]], [[Validacion-de-Modelos]], [[Overfitting-y-Underfitting]]
- Key takeaways: Hold-out, K-fold y meta-validación. Normalizar solo con parámetros de train.

## [2026-04-08] ingest | Métodos de Clasificación — Tema 2.3
- Source: raw/pdfs/sistemas-inteligentes/2.3_Clasificación.pdf
- Pages created/updated: [[2.3-clasificacion]], [[Clasificacion]]
- Key takeaways: Regresión logística, SVM, árboles, KNN, Naive Bayes. Métricas: Accuracy, Precision, Recall, F1, AUC-ROC.

## [2026-04-08] ingest | Redes Neuronales — Tema 3.1
- Source: raw/pdfs/sistemas-inteligentes/3.1_Redes_neuronales.pdf
- Pages created/updated: [[3.1-redes-neuronales]], [[Redes-Neuronales]]
- Key takeaways: Arquitectura, gradient descent, Dropout, Data Aug, Early Stopping, mini-batches, Adam.

## [2026-04-08] ingest | Visualización de Datos — Tema 1.2
- Source: raw/pdfs/sistemas-inteligentes/1.2_Visualización.pdf
- Pages created/updated: [[1.2-visualizacion]], [[Visualizacion-de-Datos]]

## [2026-04-08] ingest | Reducción de Dimensiones — Tema 1.3
- Source: raw/pdfs/sistemas-inteligentes/1.3_Reducción.pdf
- Pages created/updated: [[1.3-reduccion]], [[Reduccion-de-Dimensionalidad]]

## [2026-04-08] ingest | Introducción — IA y Aprendizaje Automático
- Source: raw/pdfs/sistemas-inteligentes/1.0_Introducción.pdf
- Pages created/updated: [[1.0-introduccion]], [[Inteligencia-Artificial]], [[Aprendizaje-Automatico]], [[Proceso-KDD]], [[John-McCarthy]], [[Frank-Rosenblatt]]

## [2026-04-08] ingest | Preprocesado y Codificación — Tema 1.1
- Source: raw/pdfs/sistemas-inteligentes/1.1_Preprocesado.pdf
- Pages created/updated: [[1.1-preprocesado]], [[Preprocesado-de-Datos]], [[Codificacion-de-Datos]]

## [2026-04-08] reset | Wiki limpiada y reiniciada desde cero
- Acción: eliminados todos los contenidos previos
