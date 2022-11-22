FROM docker.io/rockylinux:9.0

LABEL maintainer=med@mehmetemredogan.com.tr

ENV NGINX_VERSION 1.23.2
ENV PCRE_VERSION 8.45
ENV ZLIB_VERSION 1.2.13
ENV OPENSSL_VERSION 3.0.7

# .pem file
COPY serviceCert.pem /etc/pki/tls/certs/

RUN dnf update -y --setopt=install_weak_deps=False

RUN dnf groupinstall 'Development Tools' -y --setopt=install_weak_deps=False

RUN dnf install -y --setopt=install_weak_deps=False \
	nano \
	curl \
    perl \
    perl-devel \
    perl-ExtUtils-Embed \
    libxslt \
    libxslt-devel \
    libxml2 \
    libxml2-devel \
    gd \
    gd-devel

RUN cd /tmp && mkdir nginx && cd nginx && curl -L -O "https://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz" && tar zxvf nginx-*
RUN cd /tmp && mkdir pcre && cd pcre && curl -L -O "https://nav.dl.sourceforge.net/project/pcre/pcre/${PCRE_VERSION}/pcre-${PCRE_VERSION}.tar.gz" && tar zxvf pcre-*
RUN cd /tmp && mkdir zlib && cd zlib && curl -L -O "https://www.zlib.net/zlib-${ZLIB_VERSION}.tar.gz" && tar zxvf zlib-*
RUN cd /tmp && mkdir openssl && cd openssl && curl -L -O "https://www.openssl.org/source/openssl-${OPENSSL_VERSION}.tar.gz" && tar zxvf openssl-*
RUN cd /tmp && git clone https://github.com/google/ngx_brotli.git && cd ngx_brotli && git submodule update --init --recursive --progress
RUN cd /tmp && git clone https://github.com/openresty/headers-more-nginx-module.git
RUN adduser nginx --system --no-create-home --shell /bin/false --user-group

RUN cd /tmp/nginx/nginx-${NGINX_VERSION} && \
    ./configure --prefix=/etc/nginx \
        --sbin-path=/usr/sbin/nginx \ 
        --modules-path=/usr/lib64/nginx/modules \ 
        --conf-path=/etc/nginx/nginx.conf \ 
        --error-log-path=/var/log/nginx/error.log \ 
        --pid-path=/var/run/nginx.pid \ 
        --lock-path=/var/run/nginx.lock \ 
        --user=nginx \ 
        --group=nginx \ 
        --build=RockyLinux \ 
        --builddir=nginx-${NGINX_VERSION} \ 
        --with-select_module \ 
        --with-poll_module \ 
        --with-threads \ 
        --with-file-aio \ 
        --with-http_ssl_module \ 
        --with-http_v2_module \ 
        --with-http_realip_module \ 
        --with-http_addition_module \ 
        --with-http_xslt_module=dynamic \ 
        --with-http_image_filter_module=dynamic \ 
        --with-http_sub_module \ 
        --with-http_dav_module \ 
        --with-http_flv_module \ 
        --with-http_mp4_module \ 
        --with-http_gunzip_module \ 
        --with-http_gzip_static_module \ 
        --with-http_auth_request_module \ 
        --with-http_random_index_module \ 
        --with-http_secure_link_module \ 
        --with-http_degradation_module \ 
        --with-http_slice_module \ 
        --with-http_stub_status_module \ 
        --http-log-path=/var/log/nginx/access.log \ 
        --http-client-body-temp-path=/var/cache/nginx/client_temp \ 
        --http-proxy-temp-path=/var/cache/nginx/proxy_temp \ 
        --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \ 
        --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \ 
        --http-scgi-temp-path=/var/cache/nginx/scgi_temp \ 
        --with-mail=dynamic \ 
        --with-mail_ssl_module \ 
        --with-stream=dynamic \ 
        --with-stream_ssl_module \ 
        --with-stream_realip_module \ 
        --with-stream_ssl_preread_module \ 
        --with-compat \ 
        --with-pcre=../../pcre/pcre-${PCRE_VERSION} \ 
        --with-pcre-jit \ 
        --with-zlib=../../zlib/zlib-${ZLIB_VERSION} \ 
        --with-openssl=../../openssl/openssl-${OPENSSL_VERSION} \ 
        --add-module=../../ngx_brotli \ 
        --add-module=../../headers-more-nginx-module \ 
        --with-openssl-opt=no-nextprotoneg && \
        make -j$(getconf _NPROCESSORS_ONLN) && make install
RUN cd /tmp && rm -rf nginx* ngx* openssl* pcre* zlib* headers*

RUN dnf groupremove -y 'Development Tools'

RUN ln -s /usr/lib64/nginx/modules /etc/nginx/modules
RUN mkdir -p /var/cache/nginx/client_temp /var/cache/nginx/fastcgi_temp /var/cache/nginx/proxy_temp /var/cache/nginx/scgi_temp /var/cache/nginx/uwsgi_temp && chmod 700 /var/cache/nginx/* && chown nginx:root /var/cache/nginx/*
RUN mkdir -p /var/cache/nginx && nginx -t

EXPOSE 80
EXPOSE 443

STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]