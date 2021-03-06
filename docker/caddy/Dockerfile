FROM abiosoft/caddy:builder as builder

ARG version="0.11.2"
ARG plugins=""

RUN VERSION=${version} PLUGINS=${plugins} /bin/sh /usr/bin/builder.sh

FROM alpine:3.8

RUN apk add --no-cache nodejs nodejs-npm openssl

# install caddy
COPY --from=builder /install/caddy /usr/bin/caddy

# validate install
RUN /usr/bin/caddy -version
RUN /usr/bin/caddy -plugins

COPY ./builder/caddy /srv/builder
COPY ./docker/caddy/app.sh /usr/bin/docker-run-app

RUN chmod a+rx /usr/bin/docker-run-app
RUN chmod a+rx /srv/builder/makecert.sh
 
RUN cd /srv/builder && npm install
RUN npm install -g nodemon@1.18.9

RUN rm -rf /usr/share/man /tmp/* /var/cache/apk/* \
    /root/.npm /root/.node-gyp /usr/lib/node_modules/npm/man \
    /usr/includes \
    /usr/lib/node_modules/npm/doc /usr/lib/node_modules/npm/html /usr/lib/node_modules/npm/scripts

EXPOSE 80 443
WORKDIR /srv

CMD ["nodemon", "--signal", "SIGTERM", "--watch", "/srv/.env", "--exec", "/bin/sh", "/usr/bin/docker-run-app"]
