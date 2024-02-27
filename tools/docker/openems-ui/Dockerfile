FROM --platform=$BUILDPLATFORM alpine:latest AS ui_builder

RUN apk update && apk upgrade

RUN apk add --no-cache \
    npm \
    nodejs

RUN npm install -g @angular/cli

FROM --platform=$BUILDPLATFORM ui_builder AS build_ui

ARG UI_VERSION

COPY ./ /src

WORKDIR /src/ui
RUN npm install

FROM ghcr.io/linuxserver/baseimage-alpine:edge AS ui_base

RUN apk update && apk upgrade

RUN apk add --no-cache \
    nginx \
    openssl \
    npm \
    nodejs

RUN npm install -g @angular/cli

FROM ui_base

RUN mkdir -p /etc/nginx/site-templates /var/log/nginx /var/www/html/openems

COPY --from=build_ui /src/ui/ /opt/ui/src
COPY tools/docker/openems-ui/root/ /

COPY nginx.conf /etc/nginx/nginx.conf

COPY --chown=root:root tools/docker/openems-ui/nginx.conf /etc/nginx/nginx.conf

VOLUME /etc/nginx
VOLUME /var/log/nginx

ARG HOST
ARG WEBSOCKET_PORT

ENV TYPE edge
ENV REST_API_PORT 8084

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]