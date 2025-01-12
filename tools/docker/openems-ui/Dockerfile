### Build ui builder
FROM --platform=$BUILDPLATFORM alpine:latest AS ui_builder

RUN apk update && apk upgrade

RUN apk add --no-cache \
    npm \
    nodejs

RUN npm install -g @angular/cli


### Build ui files
FROM --platform=$BUILDPLATFORM ui_builder AS build_ui

ARG UI_VERSION

COPY ./ /src

WORKDIR /src/ui
RUN npm install


### Build ui base
FROM ghcr.io/linuxserver/baseimage-alpine:edge AS ui_base

RUN apk update && apk upgrade

RUN apk add --no-cache \
    nginx \
    openssl \
    npm \
    nodejs

RUN npm install -g @angular/cli

	
## Build ui container
FROM ui_base

RUN mkdir -p /etc/nginx/site-templates /var/log/nginx /var/www/html/openems

COPY --from=build_ui /src/ui/ /opt/ui/src
COPY tools/docker/openems-ui/root/ /

VOLUME /etc/nginx
VOLUME /var/log/nginx

ENV TYPE edge
ENV HOST 127.0.0.1
ENV WEBSOCKET_PORT 8085
ENV REST_API_PORT 8084

EXPOSE 80 443