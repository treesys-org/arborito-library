@title: Cableado estructurado: categorías, pruebas y buenas prácticas
@icon: 🔌
@description: Cat5e/6/6A, fibra, paneles, PoE y certificación de enlace.
@order: 2

# Cableado estructurado: la física que todo lo sostiene

Antes de hablar de routing avanzado, la **capa física** debe ser fiable: **par trenzado** categorizado, **fibra** para tramos largos o backbone, **paneos** ordenados y **pruebas** con certificador o al menos **wiremap**. Esta lección resume categorías **Cat**, **PoE**, y errores de instalación que generan **errores intermitentes** (peor que un corte limpio).

@section: Categorías de cobre

* **Cat5e:** hasta 1 Gbps en 100 m típicos; aún común en LAN antiguas.
* **Cat6 / 6A:** mejor diafonía; 6A orientado a **10 Gbps** en distancias mayores que 6 clásico.
* **Cat8:** data centers cortos, muy alta velocidad.

**AWG** y **blindaje** (U/FTP, F/FTP) importan en entornos ruidosos (industria).

@section: Fibra óptica

**Monomodo (SM)** para largas distancias y MAN/WAN; **multimodo (MM)** para campus. Conectores **LC** dominan en rack; limpieza de ferrules es crítica (polvo = atenuación).

**Atenuación y ORL** se miden con **OTDR** en enlaces largos.

@section: Normas y canal permanente

**ISO/IEC 11801**, **TIA-568**: define **canal** vs **enlace permanente**. Un patch cord mal cruzado en el rack puede pasar **continuidad** y fallar **NEXT** bajo tráfico real.

@section: PoE (Power over Ethernet)

**802.3af/at/bt** suministran potencia a APs, cámaras, teléfonos IP. Límites de **corriente** por cable y **longitud** efectiva: cable fino → caída de voltaje.

**Buenas prácticas:** switches con presupuesto PoE suficiente; no mezclar sin planificar.

@section: Pruebas mínimas

* **Wiremap** (continuidad y pares).
* **Longitud** estimada por TDR en certificadores.
* Para **gigabit**, certificación **frecuencia** (250 MHz para Cat6 según caso).

@section: Errores frecuentes

* **Radius de curvatura** de fibra violado → pérdida.
* Canaletas compartidas con **alta tensión** sin separación → diafonía.
* **Patch panels** sin etiquetar → horas perdidas en troubleshooting.

@section: Laboratorio sugerido

1. Practica crimpado RJ45 (si tienes herramienta) y verifica con tester.
2. Calcula presupuesto de enlace fibra simple: potencia del transceiver − pérdidas − margen.
3. Documenta en un diagrama **IDF/MDF** de un piso ficticio.

@quiz: ¿Qué problema típico causa fibra óptica sucia en los conectores?
@option: Aumento de velocidad
@correct: Atenuación y reflexiones que degradan el enlace o lo hacen intermitente
@option: Más ancho de banda automático
