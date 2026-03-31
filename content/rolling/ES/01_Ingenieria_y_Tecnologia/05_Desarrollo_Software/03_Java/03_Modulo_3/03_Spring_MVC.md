@title: Spring MVC y controladores REST
@icon: 🌐
@description: Mapping HTTP, códigos de estado, validación con Bean Validation.
@order: 3

# Spring MVC y controladores REST

`@RestController` combina `@Controller` + `@ResponseBody` para serializar JSON/XML directamente.

```java
@RestController
@RequestMapping("/api/pedidos")
public class PedidoApi {
    @GetMapping("/{id}")
    public PedidoDto get(@PathVariable UUID id) { ... }
}
```

Valida entrada con `@Valid` y anotaciones Bean Validation (`@NotNull`, `@Size`, …).

@section: Errores frecuentes

* Devolver `500` para errores de validación en lugar de `400` estructurado.
* Exponer entidades JPA directamente sin DTO.
@quiz: ¿Qué anotación define un controlador REST típico en Spring?
@option: @Controller sin más
@correct: @RestController
@option: @Service
