@title: Spring Data JPA: repositorios y transacciones
@icon: 🗄️
@description: JpaRepository, consultas derivadas, @Transactional.
@order: 4

# Spring Data JPA: repositorios y transacciones

**Spring Data JPA** reduce boilerplate: interfaces `JpaRepository` generan implementaciones en runtime.

```java
public interface PedidoRepository extends JpaRepository<Pedido, UUID> {
    List<Pedido> findByClienteIdAndEstado(UUID id, EstadoPedido e);
}
```

Marca servicios con `@Transactional` en la capa de aplicación; evita transacciones largas abarcando I/O externo.

@section: N+1 queries

Vigila **N+1**: usa `JOIN FETCH`, `@EntityGraph`, o proyecciones DTO.
@quiz: ¿Qué interfaz extiende repositorios JPA típicos en Spring Data?
@option: CrudRepository únicamente
@correct: JpaRepository
@option: EntityManager directo
