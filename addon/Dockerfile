# syntax=docker/dockerfile:1

# Build your fork from source
ARG REPO_URL="https://github.com/AlekseyBlokhin/govee2mqtt-f"
ARG REPO_REF="main"               # or a tag/commit, e.g. v0.0.3

FROM --platform=$BUILDPLATFORM rust:1.74-alpine AS govee2mqtt
RUN apk add --no-cache musl-dev openssl-dev pkgconfig build-base git
WORKDIR /build
RUN git clone --depth 1 --branch "$REPO_REF" "$REPO_URL" . \
 || (echo "Failed to clone $REPO_URL@$REPO_REF" && exit 1)
RUN cargo build --release

# Final add-on image
ARG BUILD_FROM
FROM $BUILD_FROM
WORKDIR /app
COPY --from=govee2mqtt /build/target/release/govee /app/govee
COPY --from=govee2mqtt /build/AmazonRootCA1.pem /app/
COPY --from=govee2mqtt /build/assets /app/assets
COPY run.sh /run.sh
CMD [ "/run.sh" ]
