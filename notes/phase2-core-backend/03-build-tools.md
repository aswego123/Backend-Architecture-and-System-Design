# Maven & Gradle

> Phase 2 · Tags: `java` `build`

## 1. The concept
Build tools that resolve dependencies, compile, test, and package Java projects.
- **Maven**: XML, convention-over-configuration, fixed lifecycle (`validate → compile → test → package → install → deploy`).
- **Gradle**: Groovy/Kotlin DSL, task graph, incremental and parallel by default. Faster on large projects.

## 2. The rule / the why
You need reproducible builds and transitive dependency resolution. Doing this by hand at scale is impossible.

## 3. Java-specific behavior
- Maven `pom.xml`: dependencies, plugins, profiles, BOM (Bill of Materials) for version alignment.
- Gradle `build.gradle(.kts)`: tasks, configurations (`implementation`, `api`, `testImplementation`), version catalogs (`libs.versions.toml`).
- Both pull from Maven Central by default.
- Lock files for reproducibility: Gradle `--write-locks`; Maven less mature here.
- Use the **Spring Boot BOM** to keep dependency versions aligned in any Spring project.

## 4. System design angle
- Monorepo support: Gradle composite builds; Maven multi-module via `<modules>`.
- Build caching (local + remote) is a productivity multiplier — Gradle has it built in.
- CI builds should run in offline mode against a private mirror for reliability.

## 5. Common mistakes / traps
- Letting transitive versions drift → "works on my machine". Pin via BOM or version catalog.
- `compile` (old Gradle) leaking dependencies → use `implementation` to keep API surface narrow.
- Running tests sequentially when they could parallelize.
- Committing build outputs (`target/`, `build/`) to git.

## 6. Revision checklist
- Maven vs Gradle in one line: ______
- What a BOM solves: ______
- Difference between `implementation` and `api`: ______
