# Rust Dockerfile

This example shows a Rust service compiled in a builder stage and copied into a slim runtime image.

## Expected Project Files

- `Cargo.toml`
- `Cargo.lock`
- `src/`
- A binary target named `app`

Change the binary name in the Dockerfile if your crate produces a different executable.

## How It Works

- `builder` stage uses the Rust toolchain to compile one release binary named `app`.
- Cargo registry and target directories use BuildKit cache mounts.
- The release binary is copied out of the cached target directory before the runtime stage.
- `runtime` stage contains only Debian slim, certificates, a non-root user, and the compiled binary.

## Build and Run

```bash
docker build -t app ./Rust
docker run --rm -p 8080:8080 -e APP_PORT=8080 app
```

## Best Practices Shown

- Rust toolchain only exists in the builder image.
- Cargo cache mounts improve rebuild speed.
- Runtime image contains only the compiled binary and required OS certificates.
- Non-root runtime user.
- Package-manager caches are removed after installation.

If your crate needs native dependencies, add only the required packages to the builder stage, such as `protobuf-compiler`, `libssl-dev`, or database client headers. For multi-platform images, use `docker buildx build --platform ...`; keep the basic Dockerfile simple unless the project truly needs custom cross-compilation logic.
