# syntax=docker/dockerfile:1.7

# Use the Go toolchain only in the builder stage.
FROM --platform=${BUILDPLATFORM} golang:1.22-alpine AS builder
ARG TARGETOS
ARG TARGETARCH
WORKDIR /src

# Copy module files first so dependency downloads are cached.
COPY go.mod go.sum ./
RUN --mount=type=cache,target=/go/pkg/mod \
    go mod download

# Copy source after dependencies to keep rebuilds fast.
COPY . .

# Use the Go build cache and produce a small static binary named app.
RUN --mount=type=cache,target=/root/.cache/go-build \
    CGO_ENABLED=0 GOOS=${TARGETOS:-linux} GOARCH=${TARGETARCH} \
    go build -trimpath -ldflags="-s -w" -o /out/app .

# Use a minimal runtime image with no Go compiler.
FROM alpine:3.20 AS runtime

# Create a dedicated non-root user for safer runtime execution.
RUN addgroup -S user && adduser -S -G user user
WORKDIR /app
ENV APP_PORT=8088

# Copy only the compiled binary and make the non-root user own it.
COPY --from=builder --chown=user:user /out/app ./app

# Drop privileges before the application starts.
USER user
EXPOSE ${APP_PORT}

# Run the compiled application.
ENTRYPOINT ["./app"]
