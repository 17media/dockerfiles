FROM        openresty/openresty:alpine as builder

WORKDIR     /rootfs

RUN         apk update

            # s6-overlay
RUN         apk add --virtual .build-deps \
                curl && \
            curl -sfL https://github.com/just-containers/s6-overlay/releases/download/v1.18.1.5/s6-overlay-amd64.tar.gz | tar xz -f - -C /rootfs

            # gcsfuse
ENV         GOPATH /go
RUN         apk add --virtual .build-deps \
                build-base \
                go \
                git && \
            go get -u github.com/googlecloudplatform/gcsfuse && \
            mkdir -p /rootfs/usr/local/bin/ && \
            mv $GOPATH/bin/gcsfuse /rootfs/usr/local/bin/.

            # auth0/nginx-jwt
RUN         apk add --virtual .build-deps \
                curl && \
            mkdir -p /rootfs/lua && \
            curl -sfL https://github.com/auth0/nginx-jwt/releases/download/v1.0.1/nginx-jwt.tar.gz | tar x -f - -C /rootfs/lua

###

FROM        openresty/openresty:alpine

ENV         S6_BEHAVIOUR_IF_STAGE2_FAILS 2
ENV         S6_CMD_WAIT_FOR_SERVICES 1

RUN         mkdir -p /usr/share/nginx/html
EXPOSE      80/tcp
ENTRYPOINT  ["/init"]
CMD         ["nginx", "-c", "/etc/nginx/nginx.conf", "-g", "daemon off;"]

RUN         apk --no-cache add --virtual .run-deps \
                fuse \
                ca-certificates

COPY        --from=builder /rootfs /
COPY        rootfs /
