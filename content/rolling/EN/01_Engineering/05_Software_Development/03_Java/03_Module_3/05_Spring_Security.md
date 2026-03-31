@title: Spring Security basics
@icon: 🛡️
@description: Filter chains, authn/z, CSRF considerations.
@order: 5

# Spring Security basics

Spring Security configures a **filter chain** for authentication and authorization.

**CSRF** protects cookie-backed session apps from cross-site form posts. Pure stateless bearer APIs often disable CSRF for those endpoints—protect tokens accordingly.
@quiz: What does a CSRF token primarily mitigate for cookie sessions?
@option: TLS encryption
@correct: Cross-site forged requests
@option: SQL injection
