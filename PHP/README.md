# PHP Dockerfile

This example shows a PHP CLI application with Composer dependencies.

## Expected Project Files

- `composer.json`
- Optional `composer.lock`
- `public/index.php`
- Application source files

## How It Works

- `vendor` stage uses the Composer image to install production dependencies.
- `runtime` stage uses the PHP CLI image and copies vendor packages plus source code.
- The final process runs as a non-root account named `user`.

## Build and Run

```bash
docker build -t app ./PHP
docker run --rm -p 8080:8080 -e APP_PORT=8080 app
```

## Best Practices Shown

- Composer is not included in the final stage.
- Production dependencies only.
- Application files owned by `user`.
- Non-root runtime user.

If your application needs PHP extensions, install only the required ones in the runtime stage or add a dedicated extension stage. Keep optional extensions such as `pcntl`, `protobuf`, or database drivers out of the generic template unless the project uses them.
