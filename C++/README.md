# C++ Dockerfile

This example shows a CMake-based C++ service using a build stage and a small runtime stage.

## Expected Project Files

- `CMakeLists.txt`
- `src/`
- A CMake install target that places the application at `bin/app`

Change the final `ENTRYPOINT` if your installed binary has a different path or name.

## How It Works

- `builder` stage installs compilers, CMake, Make, and headers.
- The application is configured, built, and installed into `/out`.
- `runtime` stage installs only minimal runtime libraries and creates a restricted `user` account.
- Only installed artifacts are copied into the final image.

## Build and Run

```bash
docker build -t app ./C++
docker run --rm -p 8080:8080 -e APP_PORT=8080 app
```

## Best Practices Shown

- Build tools stay out of the final image.
- Runtime dependencies are installed separately from build dependencies.
- Non-root runtime user.
- CMake install output gives a clean copy boundary.
- External dependencies should be version-pinned in your package manager or build system.

If your application uses libraries such as gRPC, protobuf, OpenSSL, or database clients, install the build packages in the builder stage and only the required runtime libraries in the runtime stage.
