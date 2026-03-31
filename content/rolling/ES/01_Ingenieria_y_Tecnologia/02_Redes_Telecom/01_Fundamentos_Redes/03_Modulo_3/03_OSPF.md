@title: OSPFv2: áreas, LSDB y vecinos
@icon: 🔄
@description: Tipos de LSA, DR en broadcast, coste y filtrado entre áreas.
@order: 3

# OSPF: routing link-state para empresas

**OSPF** (v2 IPv4, v3 IPv6) es un protocolo **link-state** con **áreas** jerárquicas, **LSDB** sincronizada y **SPF** para calcular caminos. Esta lección introduce **vecinos**, **tipos de área**, **métrica** basada en ancho de banda y **troubleshooting** básico.

@section: Vecinos y estados

Vecinos en enlaces punto a punto; en **broadcast** se elige **DR/BDR** para reducir adyacencias. Estados **Full** requeridos para intercambiar LSAs.

**Hello/dead timers** deben coincidir (o compatibles según tuning).

@section: Áreas y ABR

**Area 0 (backbone)** conecta otras áreas vía **ABR**. **LSAs** de tipo distinto según alcance; **resumen** entre áreas reduce tablas.

**Stub/NSSA** limitan rutas externas para sitios simples.

@section: Métrica

**Cost** = referencia / ancho de banda (Cisco por defecto). Ajusta **auto-cost** o **interface cost** para influir en SPF.

@section: Autenticación

**Plain text** (malo), **MD5**, **SHA** según plataforma. Protege contra routers no autorizados en segmento.

@section: Errores frecuentes

* **MTU mismatch** en OSPF → vecinos atascados.
* **Area** incorrecta en interfaz.
* **Passive-interface** en el lado equivocado.

@section: Laboratorio sugerido

1. Levanta OSPF en tres routers: backbone + área stub.
2. Observa `show ip ospf neighbor` y LSDB resumida.
3. Cambia costes y verifica cambio de camino preferido.

@quiz: ¿Qué función cumple el DR en un segmento OSPF broadcast?
@option: Sustituir al BGP
@correct: Reducir el número de adyacencias completas entre routers en la misma subred
@option: Cifrar todo el tráfico IP
