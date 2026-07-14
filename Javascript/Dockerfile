# syntax=docker/dockerfile:1.7

# Install Node dependencies in a separate stage for better caching.
FROM node:22-alpine AS dependencies
WORKDIR /usr/src/app

# Copy package manifests first so npm install is cached.
COPY package*.json ./
RUN --mount=type=cache,target=/root/.npm \
    npm ci --omit=dev

# Use a fresh runtime image so install-time cache is not copied in.
FROM node:22-alpine AS runtime

# Create a dedicated non-root user for safer runtime execution.
RUN addgroup -S user && adduser -S -G user user
WORKDIR /usr/src/app

# Run Node in production mode and document the expected port.
ENV NODE_ENV=production
ENV APP_PORT=8080

# Copy production dependencies and source with non-root ownership.
COPY --from=dependencies --chown=user:user /usr/src/app/node_modules ./node_modules
COPY --chown=user:user . .

# Drop privileges before the application starts.
USER user
EXPOSE ${APP_PORT}

# Start the application using the package.json start script.
ENTRYPOINT ["npm", "run", "start"]
