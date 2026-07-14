# syntax=docker/dockerfile:1.7

# Build Python wheels in a separate stage so build artifacts stay out of runtime.
FROM python:3.12-slim-bookworm AS builder
WORKDIR /build

# Improve container logging and avoid writing .pyc files.
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Copy requirements first so dependency wheels can be cached.
COPY requirements.txt ./
RUN --mount=type=cache,target=/root/.cache/pip \
    pip wheel --wheel-dir /wheels -r requirements.txt

# Use a clean slim runtime image for the final application.
FROM python:3.12-slim-bookworm AS runtime

# Create a dedicated non-root user for safer runtime execution.
RUN groupadd --system user && useradd --system --gid user --home-dir /app user
WORKDIR /app

# Keep Python behavior predictable inside containers.
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
ENV APP_PORT=1010

# Install dependencies from prebuilt wheels without using the package index.
COPY --from=builder /wheels /wheels
COPY requirements.txt ./
RUN pip install --no-cache-dir --no-index --find-links=/wheels -r requirements.txt \
    && rm -rf /wheels

# Copy application source and make the non-root user own it.
COPY --chown=user:user . .

# Drop privileges before the application starts.
USER user
EXPOSE ${APP_PORT}

# Start the Python application.
ENTRYPOINT ["python", "app.py"]
