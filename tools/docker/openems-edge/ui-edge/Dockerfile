ARG UI_VERSION=openems,openems-edge-prod,prod

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
#COPY  tools/docker/openems-edge/ui-edge/root /src

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

RUN mkdir -p /etc/nginx/site-templates /var/log/nginx /var/www/html/openems

COPY --from=build_ui /src/ui/target /var/www/html/openems
COPY tools/docker/openems-edge/ui-edge/root/ /

VOLUME /etc/nginx
VOLUME /var/log/nginx

ARG HOST
ARG WEBSOCKET_PORT

ENV EDGE_HOST 127.0.0.1
ENV EDGE_WEBSOCKET_PORT 8085
ENV EDGE_REST_API_PORT 8084

EXPOSE 80 443
CMD ["nginx", "-g", "daemon off;"]