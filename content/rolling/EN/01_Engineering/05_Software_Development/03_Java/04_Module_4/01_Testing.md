@title: JUnit 5 and Mockito
@icon: 🧪
@description: Unit vs integration, parameterized tests.
@order: 1

# JUnit 5 and Mockito

Use **JUnit Jupiter** (`org.junit.jupiter.api.Test`). **Mockito** stubs dependencies; verify collaborations explicitly.

Favor a **testing pyramid**: many fast unit tests, fewer integration tests, very few E2E.
@quiz: Which package hosts JUnit 5’s @Test annotation?
@option: org.junit only
@correct: org.junit.jupiter.api
@option: junit.framework
