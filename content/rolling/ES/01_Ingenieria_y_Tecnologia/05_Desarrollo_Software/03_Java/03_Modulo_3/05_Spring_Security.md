@title: Spring Security: filtros, autenticación y CSRF
@icon: 🛡️
@description: SecurityFilterChain, OAuth2 resource server, CORS vs CSRF.
@order: 5

# Spring Security: filtros, autenticación y CSRF

Spring Security es una cadena de **filtros servlet**. Configuras reglas por ruta, autenticación (form login, HTTP Basic, OAuth2/OIDC), y autorización (roles/scopes).

En aplicaciones **con cookies de sesión** y formularios HTML, **CSRF** importa. En APIs **stateless** puramente Bearer token, a menudo se desactiva CSRF para esos endpoints — pero entonces el token debe protegerse otras vías.

@section: Errores frecuentes

* Abrir `/**` sin autenticación “temporalmente” y olvidarlo.
* Mezclar CORS permisivo con credenciales sin validar orígenes.
@quiz: ¿Qué riesgo mitiga el token CSRF en formularios con sesión?
@option: Cifrado TLS
@correct: Petición falsificada desde otro sitio
@option: Inyección SQL
