ARG NGINX_VERSION=1.19.1

FROM nginx:$NGINX_VERSION

ARG NGINX_VERSION=1.19.1
ARG COOKIE_FLAG_VERSION=v1.1.0

RUN apt-get update \
    && apt-get install -y \
        build-essential \
        libpcre++-dev \
        zlib1g-dev \
        libgeoip-dev \
        wget \
        git

RUN cd /opt \
    && git clone --depth 1 -b $COOKIE_FLAG_VERSION --single-branch https://github.com/AirisX/nginx_cookie_flag_module.git \
    && cd /opt/nginx_cookie_flag_module \
    && git submodule update --init \
    && cd /opt \
    && wget -O - http://nginx.org/download/nginx-$NGINX_VERSION.tar.gz | tar zxfv - \
    && mv /opt/nginx-$NGINX_VERSION /opt/nginx \
    && cd /opt/nginx \
    && ./configure --with-compat --add-dynamic-module=/opt/nginx_cookie_flag_module \
    && make modules

FROM nginx:$NGINX_VERSION

COPY --from=0 /opt/nginx/objs/ngx_http_cookie_flag_filter_module.so /usr/lib/nginx/modules

RUN chmod -R 644 \
        /usr/lib/nginx/modules/ngx_http_cookie_flag_filter_module.so \
    && sed -i '1iload_module \/usr\/lib\/nginx\/modules\/ngx_http_cookie_flag_filter_module.so;' /etc/nginx/nginx.conf 