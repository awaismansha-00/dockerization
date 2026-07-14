# Ruby Dockerfile

This example shows a Ruby service using Bundler in a builder stage.

## Expected Project Files

- `Gemfile`
- `Gemfile.lock`
- `app.rb`
- Application source files

## How It Works

- `builder` stage installs build tools needed for native gems.
- Bundler is configured for deployment mode and skips development/test groups.
- `runtime` stage starts from a clean Ruby slim image without build tools.
- The final image copies installed gems and source, then runs as a restricted `user` account.

## Build and Run

```bash
docker build -t app ./Ruby
docker run --rm -p 8080:8080 -e APP_PORT=8080 app
```

## Best Practices Shown

- Build tools stay out of the final image.
- No broad file permissions such as `chmod 666`.
- Gems are installed from the lockfile for repeatable builds.
- Non-root runtime user.
- Source copied after dependency installation for better cache behavior.
