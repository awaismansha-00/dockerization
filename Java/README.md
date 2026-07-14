# Java Dockerfile

This example shows a Maven-based Java service using JDK and JRE stages.

## Expected Project Files

- `pom.xml`
- `src/` containing Java source
- A Maven build that produces one runnable jar in `target/`

For Gradle projects, use the same pattern: copy Gradle wrapper and build files first, download dependencies, then copy source and build.

## How It Works

- `dependencies` stage installs Maven and resolves dependencies before source code is copied.
- `builder` stage copies source and packages the application.
- `runtime` stage uses a JRE image instead of a full JDK.
- A restricted `app` user owns and runs the final jar.

## Build and Run

```bash
docker build -t app ./Java
docker run --rm -p 8080:8080 -e APP_PORT=8080 app
```

## Best Practices Shown

- JDK only in build stages.
- JRE-only final image.
- Dependency cache layer before source code.
- Non-root runtime user.
- No Maven cache or build output copied into the final image.
