ARG BUILD_FROM
FROM --platform=$BUILDPLATFORM rust:1.74-alpine AS govee2mqtt
RUN apk add --no-cache musl-dev openssl-dev pkgconfig build-base git
WORKDIR /build
COPY ../ .
RUN cargo build --release
FROM $BUILD_FROM
COPY run.sh /run.sh
COPY --from=govee2mqtt /app/govee /app/
COPY --from=govee2mqtt /app/assets /app/assets/
COPY --from=govee2mqtt /app/AmazonRootCA1.pem /app/
CMD [ "/run.sh" ]
