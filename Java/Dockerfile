# syntax=docker/dockerfile:1.7

# Use the JDK only for dependency resolution and compilation.
FROM eclipse-temurin:21-jdk-alpine AS dependencies
WORKDIR /build

# Install Maven in the build stage, not in the final runtime image.
RUN apk add --no-cache maven

# Copy Maven descriptors first so downloaded dependencies are cached.
COPY pom.xml ./
RUN --mount=type=cache,target=/root/.m2 \
    mvn dependency:go-offline -B

# Build the application after dependencies are available.
FROM dependencies AS builder
COPY src ./src
RUN --mount=type=cache,target=/root/.m2 \
    mvn clean package -DskipTests -B

# Use a smaller JRE image because the final container only runs the jar.
FROM eclipse-temurin:21-jre-alpine AS runtime

# Create a dedicated non-root user for safer runtime execution.
RUN addgroup -S user && adduser -S -G user user
WORKDIR /app
ENV APP_PORT=8080

# Copy the built jar and assign ownership to the runtime user.
COPY --from=builder --chown=user:user /build/target/*.jar ./app.jar

# Drop privileges before the application starts.
USER user
EXPOSE ${APP_PORT}

# Run the packaged Java application.
ENTRYPOINT ["java", "-jar", "app.jar"]
