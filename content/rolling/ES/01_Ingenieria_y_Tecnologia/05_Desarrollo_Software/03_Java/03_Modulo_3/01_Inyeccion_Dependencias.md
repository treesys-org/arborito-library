@title: Inversión de control e inyección de dependencias en Spring
@icon: 💉
@description: Beans, ApplicationContext, scopes y testing.
@order: 1

# Inversión de control e inyección de dependencias en Spring

**IoC** delega el ciclo de vida de objetos al contenedor. **Inyección por constructor** es preferible: dependencias explícitas, objetos inmutables en servicios, tests simples con mocks.

```java
@Service
public class PedidoService {
    private final PedidoRepository repo;
    public PedidoService(PedidoRepository repo) { this.repo = repo; }
}
```

@section: Scopes

`singleton` (por defecto en Spring), `prototype`, `request`/`session` en web.

@section: Errores frecuentes

* Field injection que oculta dependencias y complica tests.
* Beans con estado mutable compartido en singleton.
@quiz: ¿Qué estilo de inyección se recomienda en Spring moderno?
@option: Solo @Autowired en campos
@correct: Inyección por constructor
@option: Solo setter injection
