FROM openjdk:8-alpine

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

ENV VERSION=$VERSION

RUN echo "eula=true" > ./eula.txt
RUN echo "$VERSION" > ./version.txt

RUN mv ./config/MoreDefaultOptions/server.properties ./_server.properties

EXPOSE 25565 25575

ENTRYPOINT ["sh", "./run.sh"]