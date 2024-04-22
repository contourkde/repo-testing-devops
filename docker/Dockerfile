# Use the official nginx image as the base
FROM nginx:latest as builder

# Define ARGs and ENVs
ARG NGINX_VERSION=1.21.6
ENV NGINX_RTMP_MODULE_VERSION="1.2.1"

# Install dependencies required to build NGINX and RTMP module
RUN apt-get update && apt-get install -y \
    build-essential \
    libpcre3-dev \
    libssl-dev \
    zlib1g-dev \
    wget \
    && rm -rf /var/lib/apt/lists/*

# Download and unpack the NGINX source and the nginx-rtmp module
WORKDIR /tmp
RUN wget http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz \
    && tar zxf nginx-${NGINX_VERSION}.tar.gz \
    && wget https://github.com/arut/nginx-rtmp-module/archive/v${NGINX_RTMP_MODULE_VERSION}.tar.gz \
    && tar zxf v${NGINX_RTMP_MODULE_VERSION}.tar.gz

# Compile NGINX with the nginx-rtmp module
WORKDIR /tmp/nginx-${NGINX_VERSION}
RUN ./configure \
    --prefix=/usr/local/nginx \
    --with-http_ssl_module \
    --add-dynamic-module=../nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION} \
    && make \
    && make install

# Final base image
FROM nginx:latest

# Copy over the compiled modules and configuration from the builder
COPY --from=builder /usr/local/nginx /usr/local/nginx
COPY assets/etc/nginx.conf /etc/nginx/nginx.conf

# Create necessary directories and set permissions
RUN mkdir -p /var/lib/streaming/hls /var/lib/streaming/dash \
    && chown -R nginx:nginx /usr/local/nginx /var/lib/streaming

# Expose the necessary ports
EXPOSE 1935 8080 8443

# Set the CMD
CMD ["nginx", "-g", "daemon off;"]
