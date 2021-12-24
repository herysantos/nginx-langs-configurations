FROM alpine:latest
MAINTAINER Hery Santos <linkedin.com/in/herysantos>

# Run the command below to see available versions
# curl -s https://api.github.com/repos/nginx/nginx/tags | jq -r '.[]|.name' | awk -F '-' '{print $2}'

ENV NGINX_VER=1.20.2

RUN pwd

#  download, compile and install Nginx and its dependencies
RUN apk update && apk add --no-cache build-base ca-certificates linux-headers openssl-dev pcre-dev wget zlib-dev musl openssl pcre zlib &&\
  wget http://nginx.org/download/nginx-${NGINX_VER}.tar.gz -O /tmp/nginx-${NGINX_VER}.tar.gz &&\
  cd /tmp &&\
  tar zxvf /tmp/nginx-${NGINX_VER}.tar.gz

WORKDIR /tmp/nginx-${NGINX_VER}
  
RUN ./configure \
    --sbin-path=/usr/sbin/nginx \
    --conf-path=/etc/nginx/nginx.conf \
    --error-log-path=/var/log/nginx/error.log \
    --pid-path=/nginx.pid \
    --lock-path=/var/lock/nginx.lock \
    --http-log-path=/var/log/nginx/access.log \
    --with-http_dav_module \
    --http-client-body-temp-path=/var/lib/nginx/body \
    --with-http_ssl_module \
    --with-http_realip_module \
    --http-proxy-temp-path=/var/lib/nginx/proxy \
    --with-http_stub_status_module \
    --http-fastcgi-temp-path=/var/lib/nginx/fastcgi \
    --with-file-aio \
    --with-http_addition_module \
    --with-http_auth_request_module \
    --with-http_flv_module \
    --with-http_gunzip_module \
    --with-http_gzip_static_module \
    --with-http_mp4_module \
    --with-http_sub_module \
    --with-http_v2_module \
    --with-stream \
    --with-stream_realip_module \
    --with-stream_ssl_module \
    --with-stream_ssl_preread_module \
    --with-threads \
    --user=www-data \
    --group=www-data

RUN make &&\
    make install 

RUN mkdir -p /var/lib/nginx /etc/nginx/sites-enabled /etc/nginx/sites-available /etc/nginx/streams-enabled /etc/nginx/streams-available /var/www &&\
    ln -sf /etc/nginx/sites-available/default /etc/nginx/sites-enabled/default &&\
    ln -sf /dev/stdout /var/log/nginx/access.log &&\
    ln -sf /dev/stderr /var/log/nginx/error.log &&\
    deluser $(grep ':33:' /etc/passwd | awk -F ':' '{print $1}') || true  &&\
    delgroup $(grep '^www-data:' /etc/group | awk -F ':' '{print $1}') || true &&\
    addgroup -g 33 www-data &&\
    adduser -D -u 33 -G www-data -s /sbin/nologin -H -h /var/www www-data &&\
    chown -R www-data:www-data /var/www

RUN rm /etc/nginx/*.default &&\
    rm -rf /tmp/nginx-${NGINX_VER} /tmp/nginx-${NGINX_VER}.tar.gz &&\
    apk del build-base linux-headers openssl-dev pcre-dev wget zlib-dev

# include my default config files
COPY configs/nginx.conf /etc/nginx/
COPY configs/default /etc/nginx/sites-available/default

# add an index.html
COPY index.html /var/www/index.html

WORKDIR /var/www

EXPOSE 80 443
CMD ["nginx","-c","/etc/nginx/nginx.conf"]