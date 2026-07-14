# Install Composer dependencies in a separate stage so Composer is not in runtime.
FROM composer:2.7 AS vendor
WORKDIR /app

# Copy Composer files first so dependency install is cached.
COPY composer.* ./
RUN composer install \
    --no-dev \
    --no-interaction \
    --no-progress \
    --prefer-dist \
    --optimize-autoloader

# Use the PHP CLI runtime image for the final application.
FROM php:8.3-cli-bookworm AS runtime

# Create a dedicated non-root user for safer runtime execution.
RUN groupadd --system user && useradd --system --gid user --home-dir /var/www user
WORKDIR /var/www
ENV APP_PORT=8080

# Copy production dependencies and source with non-root ownership.
COPY --from=vendor --chown=user:user /app/vendor ./vendor
COPY --chown=user:user . .

# Drop privileges before the application starts.
USER user
EXPOSE ${APP_PORT}

# Start the PHP entrypoint.
ENTRYPOINT ["php", "public/index.php"]
