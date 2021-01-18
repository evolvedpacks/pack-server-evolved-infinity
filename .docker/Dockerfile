# --- BUILD HEALTHCHECK TOOL STAGE ---------------------------------

FROM golang:1.15-alpine AS healthcheck-build
WORKDIR /build

RUN apk add git
RUN git clone https://github.com/evolvedpacks/healthcheck \
        --branch master --depth 1 .
RUN go mod download
RUN go build -v -o healthcheck ./cmd/healthcheck/main.go

# --- FINAL IMAGE STAGE --------------------------------------------

FROM openjdk:8-alpine AS final

ARG VERSION=latest

ENV XMS=1G
ENV XMX=3G

RUN apk add curl git

WORKDIR /var/server

COPY ./.docker/scripts/run.sh ./run.sh
COPY ./config ./config
COPY ./libraries ./libraries
COPY ./mods ./mods
COPY ./forge-* ./forge.jar
COPY ./minecraft_server.1.12.2.jar ./minecraft_server.1.12.2.jar
COPY ./server-icon.png ./server-icon.png

COPY --from=healthcheck-build /build/healthcheck /bin/healthcheck
# 90 Retries * 10s -> 15 Minutes Startup Time Assumption
HEALTHCHECK --interval=10s --timeout=10s --retries=90 \
    CMD /bin/healthcheck -addr localhost:25565 -validateResponse -silent

ENV VERSION=$VERSION

RUN echo "eula=true" > ./eula.txt
RUN echo "$VERSION" > ./version.txt

RUN mv ./config/MoreDefaultOptions/server.properties ./_server.properties

EXPOSE 25565 25575

ENTRYPOINT ["sh", "./run.sh"]