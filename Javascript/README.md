# JavaScript Dockerfile

This example shows a Node.js service using production dependencies only.

## Expected Project Files

- `package.json`
- `package-lock.json`
- Application source
- A `start` script in `package.json`

## How It Works

- `dependencies` stage copies `package*.json` first and runs `npm ci --omit=dev`.
- BuildKit caches the npm download cache.
- `runtime` stage copies production `node_modules` and the application source.
- The final image runs as a non-root account named `user`.

## Build and Run

```bash
docker build -t app ./Javascript
docker run --rm -p 8080:8080 -e APP_PORT=8080 app
```

## Best Practices Shown

- Reproducible installs with `npm ci`.
- Development dependencies omitted from the final image.
- Non-root runtime user.
- Ownership-aware copies with `--chown=user:user`.
- `NODE_ENV=production` set in the runtime image.
