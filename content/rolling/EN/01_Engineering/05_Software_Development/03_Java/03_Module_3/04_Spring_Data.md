@title: Spring Data JPA repositories
@icon: 🗄️
@description: JpaRepository, derived queries, transactions.
@order: 4

# Spring Data JPA repositories

Extend `JpaRepository<Entity, Id>` for CRUD + query derivation.

Keep `@Transactional` boundaries around domain operations; watch **N+1** fetch issues.
@quiz: Which Spring Data interface commonly backs JPA repositories?
@option: CrudRepository only
@correct: JpaRepository
@option: Raw EntityManager
