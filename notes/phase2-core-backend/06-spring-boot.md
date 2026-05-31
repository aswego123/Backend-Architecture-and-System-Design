# Spring Boot: auto-config & starters

> Phase 2 · Tags: `java` `spring`

## 1. The concept
Spring Boot = Spring + opinionated defaults + auto-configuration + embedded server. You add `spring-boot-starter-web` and you have a running HTTP service with Tomcat in two lines.

**Auto-configuration**: classes annotated `@AutoConfiguration` (with `@ConditionalOn*` guards) configure beans automatically *if* matching classes/properties are on the classpath.

**Starters**: curated dependency bundles (`spring-boot-starter-data-jpa` pulls JPA, Hibernate, transaction manager, JDBC, HikariCP).

## 2. The rule / the why
Removes 90% of the boilerplate of "wiring a Spring app". Conventions cover the common path; you override only what's different.

## 3. Java-specific behavior
- Entry point: `@SpringBootApplication` (= `@Configuration` + `@ComponentScan` + `@EnableAutoConfiguration`).
- Configuration sources (highest wins): command-line args > env vars > `application-{profile}.yml` > `application.yml` > defaults.
- Inspect what auto-config did: `--debug` flag prints positive/negative matches.
- `@ConfigurationProperties("app.foo")` for typed config binding (preferred over `@Value`).
- Actuator endpoints for ops (`/actuator/health`, `/actuator/metrics`).

## 4. System design angle
- Embedded Tomcat/Jetty/Undertow means the app *is* the deployable artifact — fits Docker and 12-factor cleanly.
- Externalized config + profiles map directly to ConfigMaps/Secrets in Kubernetes.
- Actuator + Micrometer gives instant Prometheus metrics.

## 5. Common mistakes / traps
- Fighting auto-config instead of disabling pieces with `exclude = {...}`.
- Putting secrets in `application.yml` committed to git — use env vars or a secret store.
- Using `@Value` for structured config — switch to `@ConfigurationProperties` for type safety.
- Exposing actuator publicly without auth — leaks heap dumps, env vars, etc.

## 6. Revision checklist
- What auto-configuration is in one sentence: ______
- The property override order: ______
- One actuator endpoint to expose, one to secure: ______
- `@Value` vs `@ConfigurationProperties`: ______
