# Activity Log

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
