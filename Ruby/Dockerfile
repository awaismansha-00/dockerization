# Use the Ruby image only for installing gems that may need native build tools.
FROM ruby:3.2.2-slim AS builder
WORKDIR /app

# Install build tools for native gems, then remove package-manager lists.
RUN apt-get update \
    && apt-get install --no-install-recommends -y build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy gem manifests first so bundle install is cached.
COPY Gemfile Gemfile.lock ./
RUN bundle config set deployment true \
    && bundle config set without "development test" \
    && bundle install

# Use a clean Ruby runtime image without build tools.
FROM ruby:3.2.2-slim AS runtime

# Create a dedicated non-root user for safer runtime execution.
RUN groupadd --system user && useradd --system --gid user --home-dir /app user
WORKDIR /app
ENV APP_PORT=8080

# Copy installed gems and source into the runtime image.
COPY --from=builder /usr/local/bundle /usr/local/bundle
COPY --chown=user:user . .

# Drop privileges before the application starts.
USER user
EXPOSE ${APP_PORT}

# Start the Ruby application through Bundler.
ENTRYPOINT ["bundle", "exec", "ruby", "app.rb"]
