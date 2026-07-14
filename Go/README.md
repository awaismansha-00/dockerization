# Go Dockerfile

This example shows how to build a small Go container image with a compiled binary.

## Expected Project Files

- `go.mod`
- `go.sum`
- Go source files for a `main` package

## How It Works

- `builder` stage uses the official Go Alpine image.
- `go.mod` and `go.sum` are copied before the rest of the source, allowing dependency downloads to be cached.
- BuildKit cache mounts speed up module and compiler cache reuse.
- The binary is built with `CGO_ENABLED=0`, `-trimpath`, and stripped linker flags.
- `runtime` stage uses Alpine, creates a restricted `app` user, and copies only the final binary.

## Build and Run

```bash
docker build -t app ./Go
docker run --rm -p 8088:8088 -e APP_PORT=8088 app
```

## Best Practices Shown

- Multi-stage build.
- Small runtime image.
- Non-root runtime user.
- Cached module downloads.
- Static binary suitable for minimal containers.
