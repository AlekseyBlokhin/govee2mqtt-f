# syntax=docker/dockerfile:1

# --- Global args (must be before any FROM if used in FROM) ---
ARG BUILD_FROM
ARG REPO_URL="https://github.com/AlekseyBlokhin/govee2mqtt-f"
ARG REPO_REF="main"   # set a tag/branch/commit via config.yaml -> args

# --- Build stage: compile your fork from source ---
FROM --platform=$BUILDPLATFORM rust:1.74-alpine AS govee2mqtt

RUN apk add --no-cache musl-dev openssl-dev pkgconfig build-base git

WORKDIR /build
# Clone your repo at the ref provided (Supervisor will pass args)
# (If clone fails, exit with message so it's obvious)
RUN git clone --depth 1 --branch "$REPO_REF" "$REPO_URL" . \
 || (echo "Failed to clone $REPO_URL at $REPO_REF" && exit 1)

# Build release binary
RUN cargo build --release

# --- Final stage: Home Assistant base image for the add-on ---
FROM $BUILD_FROM

WORKDIR /app

# Copy the compiled binary and runtime assets from the build stage
COPY --from=govee2mqtt /build/target/release/govee /app/govee
COPY --from=govee2mqtt /build/AmazonRootCA1.pem /app/
COPY --from=govee2mqtt /build/assets /app/assets

# Entrypoint from the add-on context
COPY run.sh /run.sh

CMD [ "/run.sh" ]
