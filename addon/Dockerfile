# syntax=docker/dockerfile:1

# Home Assistant passes BUILD_FROM; we just need to declare it before any FROM.
ARG BUILD_FROM

# --- Hardcode your fork + ref here (change these two lines if needed) ---
ARG REPO_URL="https://github.com/AlekseyBlokhin/govee2mqtt-f"
ARG REPO_REF="main"   # or a tag like v0.0.5, or a commit SHA

# ---------- Build stage: compile your fork on Debian (glibc) ----------
FROM --platform=$BUILDPLATFORM rust:1.74-bookworm AS build

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      ca-certificates git pkg-config libssl-dev build-essential \
 && rm -rf /var/lib/apt/lists/*

WORKDIR /src

# Clone your repo at the ref above (hardcoded here; no build args needed)
RUN git clone --depth 1 --branch "$REPO_REF" "$REPO_URL" . \
 || (echo "Failed to clone $REPO_URL at $REPO_REF" && exit 1)

# Build release binary
RUN cargo build --release

# ---------- Final stage: Home Assistant base image ----------
FROM $BUILD_FROM
WORKDIR /app

# Copy compiled binary and runtime assets
COPY --from=build /src/target/release/govee /app/govee
COPY --from=build /src/AmazonRootCA1.pem /app/
COPY --from=build /src/assets /app/assets

# Entrypoint script (this file is in addon/ so it is in the build context)
COPY run.sh /run.sh

CMD ["/run.sh"]
