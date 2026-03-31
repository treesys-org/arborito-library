@title: Tablas hash: funciones, colisiones y rehashing
@icon: #️⃣
@description: encadenamiento vs open addressing, cargas y tablas de símbolos.
@order: 4

# Tablas hash: esperanza O(1) con buen diseño

Una **tabla hash** mapea claves a valores con función **hash** y **resolución de colisiones** (encadenamiento, **open addressing** con sondas lineales/cuadráticas/doble). **Factor de carga** controla rehashing. Esta lección explica **uniformidad** y **ataques** de colisión (hashDoS mitigado con **salt**).

@section: Funciones

Murmur, xxHash, **criptográficas** (SHA) para integridad, no siempre para tablas.

@section: open addressing

Requiere **tombstones** al borrar; **robin hood hashing** reduce varianza de probes.

@section: Laboratorio sugerido

1. Implementa hash map con encadenamiento en C.
2. Mide colisiones con `strings` aleatorias vs adversarias.
3. Compara `unordered_map` vs `map` en tiempo para 1e6 inserciones.

@quiz: ¿Qué fenómeno aumenta probes y degrada rendimiento si el factor de carga es alto?
@option: Menos memoria
@correct: Más colisiones y rehashing frecuente
@option: Menor endianness
