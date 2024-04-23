# Use Ubuntu as the base image
FROM ubuntu:20.04 as builder

# Avoid prompts from apt
ENV DEBIAN_FRONTEND=noninteractive

# Install necessary packages
RUN apt-get update && apt-get install -y \
    build-essential \
    libpcre3-dev \
    libssl-dev \
    zlib1g-dev \
    wget \
    ca-certificates

# Define ARGs for versions
ARG NGINX_VERSION=1.21.6
ARG NGINX_RTMP_MODULE_VERSION=1.2.1

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
    --add-module=../nginx-rtmp-module-${NGINX_RTMP_MODULE_VERSION} \
    && make \
    && make install

# Final base image
FROM ubuntu:20.04

# Copy over the compiled binaries and configuration from the builder
COPY --from=builder /usr/local/nginx /usr/local/nginx

# Copy any additional custom configuration files (adjust path as needed)
# COPY nginx.conf /usr/local/nginx/conf/nginx.conf

# Set PATH environment variable
ENV PATH="/usr/local/nginx/sbin:$PATH"

# Expose necessary ports
EXPOSE 1935 80 443

# Set the CMD
CMD ["nginx", "-g", "daemon off;"]
