# Python Dockerfile

This example shows a Python service using wheel building and a slim runtime image.

## Expected Project Files

- `requirements.txt`
- `app.py`
- Application source files

## How It Works

- `builder` stage builds Python wheels from `requirements.txt`.
- `runtime` stage installs those wheels without reaching back out to the package index.
- Python bytecode files are disabled and output is unbuffered for container logs.
- A system `user` account runs the application instead of root.

## Build and Run

```bash
docker build -t app ./Python
docker run --rm -p 1010:1010 -e APP_PORT=1010 app
```

## Best Practices Shown

- Dependency build separated from runtime.
- `pip install --no-cache-dir` in the final image.
- Non-root runtime user.
- Source copied after dependencies for better caching.
- Slim Debian runtime base.

For larger projects, consider installing into a virtual environment in the builder stage and copying that environment into the runtime stage.
