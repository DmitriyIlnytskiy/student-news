# ---- Stage 1: build ----
FROM maven:3.10-eclipse-temurin-27-alpine AS builder
WORKDIR /build

# Dependencies first: this layer is only rebuilt when pom.xml changes
COPY pom.xml .
RUN mvn -B -q dependency:go-offline

# Source after: changing Java code only invalidates from here down
COPY src ./src
RUN mvn -B -q clean package -DskipTests

# ---- Stage 2: runtime (JRE only) ----
FROM eclipse-temurin:27-jre-alpine
WORKDIR /app

RUN addgroup -S appgroup && adduser -S appuser -G appgroup
RUN mkdir -p /data/avatars && chown -R appuser:appgroup /data/avatars

COPY --from=builder /build/target/*.jar app.jar
USER appuser:appgroup

EXPOSE 8090
ENTRYPOINT ["java", "-jar", "app.jar"]