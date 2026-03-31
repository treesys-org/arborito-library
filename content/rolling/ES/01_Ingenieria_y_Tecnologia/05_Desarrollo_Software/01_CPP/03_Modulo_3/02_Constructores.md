@title: Constructores, delegación e inicialización
@icon: 🧱
@description: Lista de inicialización, =default/delete, NRVO.
@order: 2

# Construcción segura y eficiente

**Constructores** inicializan invariantes. La **lista de inicialización de miembros** evita asignaciones dobles. **`=default`** y **`=delete`** controlan operaciones especiales. **RVO/NRVO** evitan copias en retornos locales.

@section: Inicialización uniforme

`{}` previene **narrowing** en muchos casos.

@section: Regla de cero

Si solo necesitas recursos gestionados por miembros RAII, evita implementar destructor copia manualmente.

@quiz: ¿Por qué inicializar miembros en la lista y no en el cuerpo del constructor?
@option: Es obligatorio legalmente
@correct: Para construir directamente miembros y evitar trabajo extra o estados temporales inválidos
@option: Porque el cuerpo no existe
