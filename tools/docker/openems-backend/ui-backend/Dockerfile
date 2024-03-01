ARG UI_VERSION=openems,openems-backend-prod,prod

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
#COPY tools/docker/ui-backend/root /src

WORKDIR /src/ui
RUN npm install
RUN ng build -c "${UI_VERSION}"


### Build ui base
FROM ghcr.io/linuxserver/baseimage-alpine:edge AS ui_base

RUN apk update && apk upgrade

RUN apk add --no-cache \
    nginx \
    openssl
	
## Build ui container
FROM ui_base

RUN mkdir -p /etc/nginx /var/log/nginx /var/www/html/openems 

COPY --from=build_ui /src/ui/target /var/www/html/openems
COPY tools/docker/openems-backend/ui-backend/root/ /

VOLUME /etc/nginx
VOLUME /var/log/nginx

ARG HOST
ARG WEBSOCKET_PORT

EXPOSE 80 443