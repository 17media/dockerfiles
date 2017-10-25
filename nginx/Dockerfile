FROM    nginx:mainline-alpine as builder

ARG     NGINX_HEADERS_MORE_MODULE_VERSION=0.32

WORKDIR /usr/src/nginx
RUN     apk add --no-cache --virtual .build-deps \
            build-base \
            curl \
            tar \
            zlib-dev \
            pcre-dev
RUN     curl -fSL --output - http://nginx.org/download/nginx-1.13.5.tar.gz | tar xz -C /usr/src/nginx --strip-components 1
RUN     mkdir -p /tmp/headers-more-nginx-module && \
        curl -fSL --output - https://github.com/openresty/headers-more-nginx-module/archive/v$NGINX_HEADERS_MORE_MODULE_VERSION.tar.gz | tar xz -C /tmp/headers-more-nginx-module --strip-components 1

RUN     ./configure --with-debug \
            --with-compat \
            --add-dynamic-module=/tmp/headers-more-nginx-module && \
        make -j$(getconf _NPROCESSORS_ONLN) && \
        install -m755 objs/ngx_http_headers_more_filter_module.so /usr/lib/nginx/modules/ngx_http_headers_more_filter_module-debug.so && \
        strip /usr/lib/nginx/modules/*.so
RUN     ./configure \
            --with-compat \
            --add-dynamic-module=/tmp/headers-more-nginx-module && \
	    make -j$(getconf _NPROCESSORS_ONLN) && \
        install -m755 objs/ngx_http_headers_more_filter_module.so /usr/lib/nginx/modules/ngx_http_headers_more_filter_module.so && \
        strip /usr/lib/nginx/modules/*.so

###

FROM    nginx:mainline-alpine

COPY    --from=builder /usr/lib/nginx/modules/ngx_http_headers_more_filter_module*.so /usr/lib/nginx/modules/
