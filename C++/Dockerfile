# Use the compiler and CMake only in the builder stage.
FROM alpine:3.20 AS builder
WORKDIR /src

# Install build-time tools and headers needed to compile the application.
RUN apk add --no-cache \
    cmake \
    g++ \
    make \
    linux-headers

# Copy build configuration and source, then install compiled output into /out.
COPY CMakeLists.txt ./
COPY src ./src
RUN cmake -S . -B build \
    -DCMAKE_CXX_STANDARD=17 \
    -DCMAKE_BUILD_TYPE=Release \
    -DBUILD_TESTING=OFF \
    && cmake --build build --parallel \
    && cmake --install build --prefix /out

# Use a small runtime image with only the libraries needed to run the binary.
FROM alpine:3.20 AS runtime

# Install the C++ runtime library and create a non-root user.
RUN apk add --no-cache libstdc++ \
    && addgroup -S user \
    && adduser -S -G user user

WORKDIR /app
ENV APP_PORT=8080

# Copy only installed artifacts and make the non-root user own them.
COPY --from=builder --chown=user:user /out/ ./

# Drop privileges before the application starts.
USER user
EXPOSE ${APP_PORT}

# Run the installed application binary.
ENTRYPOINT ["./bin/app"]
