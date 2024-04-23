FROM codeworksio/nginx:1.15.8-20190219

# Set build arguments and environment variables
ARG APT_PROXY
ARG APT_PROXY_SSL
ENV NGINX_RTMP_MODULE_VERSION="1.2.1"
ENV NGINX_VERSION="1.15.8"
ENV SYSTEM_USER=nginx

# Setup APT proxies if provided
RUN set -ex; \
    if [ -n "$APT_PROXY" ]; then echo "Acquire::http { Proxy \"http://${APT_PROXY}\"; };" > /etc/apt/apt.conf.d/00proxy; fi; \
    if [ -n "$APT_PROXY_SSL" ]; then echo "Acquire::https { Proxy \"https://${APT_PROXY_SSL}\"; };" > /etc/apt/apt.conf.d/00proxy; fi;


# Install necessary packages
RUN set -ex; \
    apt-get --yes update; \
    apt-get --yes install build-essential libpcre3-dev libssl-dev zlib1g-dev ffmpeg curl;

# Download, unpack and build NGINX with the RTMP module
RUN set -ex; \
    cd /tmp; \
    curl -L "https://nginx.org/download/nginx-$NGINX_VERSION.tar.gz" -o nginx-$NGINX_VERSION.tar.gz; \
    tar -xf nginx-$NGINX_VERSION.tar.gz; \
    curl -L "https://github.com/arut/nginx-rtmp-module/archive/v${NGINX_RTMP_MODULE_VERSION}.tar.gz" -o nginx-rtmp-module-$NGINX_RTMP_MODULE_VERSION.tar.gz; \
    tar -xf nginx-rtmp-module-$NGINX_RTMP_MODULE_VERSION.tar.gz; \
    cd nginx-$NGINX_VERSION; \
    ./configure \
        $(nginx -V 2>&1 | grep 'configure arguments:' | sed 's/configure arguments://g') \
        --add-dynamic-module=/tmp/nginx-rtmp-module-$NGINX_RTMP_MODULE_VERSION; \
    make modules; \
    mkdir -p /usr/local/nginx/modules; \
    cp objs/ngx_rtmp_module.so /usr/local/nginx/modules;

# Set up directories for streaming
RUN set -ex; \
    mkdir -p /var/lib/streaming/hls /var/lib/streaming/dash; \
    groupadd -r nginx && useradd -r -g nginx nginx || true; \
    chown -R nginx:nginx /usr/local/nginx/modules /var/lib/streaming;


# Clean up unnecessary files and remove build dependencies
RUN set -ex; \
    apt-get purge --yes --auto-remove build-essential libpcre3-dev libssl-dev zlib1g-dev; \
    apt-get clean; \
    rm -rf /tmp/* /var/tmp/* /var/lib/apt/lists/* /var/cache/apt/*; \
    rm -f /etc/apt/apt.conf.d/00proxy;

# Copy configuration files and assets
COPY config/ /etc/nginx/
COPY assets/ /usr/share/nginx/html/

# Volume and port configuration
VOLUME ["/var/lib/streaming"]
EXPOSE 1935 8080 8443

# Command to run NGINX
CMD ["nginx", "-g", "daemon off;"]

### METADATA ###################################################################
ARG IMAGE
ARG BUILD_DATE
ARG VERSION
ARG VCS_REF
ARG VCS_URL
LABEL \
    org.label-schema.name=$IMAGE \
    org.label-schema.build-date=$BUILD_DATE \
    org.label-schema.version=$VERSION \
    org.label-schema.vcs-ref=$VCS_REF \
    org.label-schema.vcs-url=$VCS_URL \
    org.label-schema.schema-version="2.0"
