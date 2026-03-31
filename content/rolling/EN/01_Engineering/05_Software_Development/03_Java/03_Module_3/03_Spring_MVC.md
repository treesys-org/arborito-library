@title: Spring MVC and REST controllers
@icon: 🌐
@description: HTTP mapping, status codes, validation.
@order: 3

# Spring MVC and REST controllers

`@RestController` serves REST resources with message converters.

```java
@RestController
@RequestMapping("/api/orders")
public class OrderApi {
    @GetMapping("/{id}")
    public OrderDto get(@PathVariable UUID id) { ... }
}
```

Validate request bodies with `@Valid`.
@quiz: Which annotation typically marks a Spring REST controller?
@option: @Controller alone
@correct: @RestController
@option: @Service
