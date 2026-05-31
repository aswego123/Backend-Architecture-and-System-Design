# Docker for Java services

> Phase 2 · Tags: `devops` `docker`

## 1. The concept
Docker packages your app + dependencies into a portable **image** that runs as a **container** (isolated process). Containers share the host kernel but have their own filesystem, network namespace, and resource limits.

## 2. The rule / the why
- "Works on my machine" dies.
- Same artifact runs on a laptop, CI, staging, prod.
- Kubernetes runs containers — Docker is the on-ramp.

## 3. Java-specific behavior (a good Dockerfile)

```dockerfile
# --- build stage ---
FROM eclipse-temurin:21-jdk AS build
WORKDIR /src
COPY . .
RUN ./gradlew --no-daemon bootJar

# --- runtime stage ---
FROM eclipse-temurin:21-jre AS runtime
WORKDIR /app
COPY --from=build /src/build/libs/*.jar app.jar
EXPOSE 8080
ENV JAVA_OPTS="-XX:MaxRAMPercentage=75 -XX:+ExitOnOutOfMemoryError"
ENTRYPOINT ["sh","-c","exec java $JAVA_OPTS -jar app.jar"]
```

Tips:
- **Multi-stage** keeps the runtime image small (no JDK, no build tools).
- **JRE base image** (or distroless for security). Pin OS + JVM version.
- Use **Spring Boot layered jars** + `--layers` for better cache reuse on rebuilds.
- One process per container — no init systems, no cron.

## 4. System design angle
- 12-factor: config from env vars, logs to stdout, stateless processes.
- Health endpoints (`/actuator/health/liveness`, `/readiness`) wired to container probes.
- Image size affects deploy speed and attack surface — distroless or Alpine where possible.
- Don't run as root in the container.

## 5. Common mistakes / traps
- One giant single-stage Dockerfile that ships build tools to prod.
- Not using `.dockerignore` → bloats build context.
- Hardcoded config baked into the image instead of env vars.
- `latest` tag in production → no reproducibility.
- Letting JVM use only 25% of container memory because `MaxRAMPercentage` not set.

## 6. Revision checklist
- Why multi-stage builds: ______
- One JVM flag to add for containers: ______
- 12-factor in one line: ______
- Two security best practices: ______
