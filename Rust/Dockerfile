# syntax=docker/dockerfile:1.7

# Use the Rust toolchain only in the builder stage.
FROM rust:1.76-bookworm AS builder
WORKDIR /app

# Copy manifests first so dependency compilation can be cached.
COPY Cargo.toml Cargo.lock ./
COPY src ./src

# Use Cargo caches for faster rebuilds and copy the release binary out of target.
RUN --mount=type=cache,target=/usr/local/cargo/registry \
    --mount=type=cache,target=/app/target \
    cargo build --release \
    && cp target/release/app /app/app

# Use a slim runtime image without the Rust compiler.
FROM debian:bookworm-slim AS runtime

# Create a dedicated non-root user and install certificates for HTTPS clients.
RUN groupadd --system user && useradd --system --gid user --home-dir /app user \
    && apt-get update \
    && apt-get install --no-install-recommends -y ca-certificates \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
ENV APP_PORT=8080

# Copy only the compiled binary and make the non-root user own it.
COPY --from=builder --chown=user:user /app/app ./app

# Drop privileges before the application starts.
USER user
EXPOSE ${APP_PORT}

# Run the compiled application.
ENTRYPOINT ["./app"]
