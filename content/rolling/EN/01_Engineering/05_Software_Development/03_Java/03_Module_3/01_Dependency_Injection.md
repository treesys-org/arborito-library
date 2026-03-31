@title: Inversion of control and Spring dependency injection
@icon: 💉
@description: Beans, scopes, constructor injection.
@order: 1

# Inversion of control and Spring dependency injection

Prefer **constructor injection** for immutable, testable services.

```java
@Service
public class OrderService {
    private final OrderRepository repo;
    public OrderService(OrderRepository repo) { this.repo = repo; }
}
```

@section: Pitfalls

* Hidden dependencies via field injection.
* Mutable singleton state.
@quiz: Which injection style is preferred in modern Spring?
@option: Field @Autowired only
@correct: Constructor injection
@option: Setter-only injection
