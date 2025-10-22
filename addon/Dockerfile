# syntax=docker/dockerfile:1

# HA Supervisor injects BUILD_FROM; declare it before any FROM that uses it
ARG BUILD_FROM

# ---------- build stage: compile your fork on Debian (glibc) ----------
FROM --platform=$BUILDPLATFORM rust:1.74-bookworm AS build

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      ca-certificates git pkg-config libssl-dev build-essential \
 && rm -rf /var/lib/apt/lists/*

WORKDIR /src

# Hardcode your repo + branch/tag here. No ARGs needed.
RUN git clone --depth 1 --branch main \
    https://github.com/AlekseyBlokhin/govee2mqtt-f . \
 || (echo "Failed to clone your fork" && exit 1)

# Build the release binary
RUN cargo build --release

# ---------- final stage: Home Assistant base image ----------
FROM $BUILD_FROM
WORKDIR /app

# Copy compiled binary + runtime assets from build stage
COPY --from=build /src/target/release/govee /app/govee
COPY --from=build /src/AmazonRootCA1.pem /app/
COPY --from=build /src/assets /app/assets

# Entrypoint from the add-on context (present in addon/ folder)
COPY run.sh /run.sh

CMD ["/run.sh"]
