FROM mcr.microsoft.com/devcontainers/base:ubuntu-22.04

ARG DEBIAN_FRONTEND=noninteractive
ARG USERNAME=vscode
ARG RUST_VERSION=1.91.0
ARG NGINX_VERSION=1.29.3

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        pkg-config \
        libssl-dev \
        libpcre2-dev \
        zlib1g-dev \
        ca-certificates \
        curl \
        git \
        libclang-dev \
        cpanminus \
    && cpanm install IO::Socket::SSL \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN su - ${USERNAME} -c "curl -fsSL https://sh.rustup.rs | sh -s -- -y --default-toolchain ${RUST_VERSION}" \
    && su - ${USERNAME} -c "~/.cargo/bin/rustup component add --toolchain ${RUST_VERSION} rustfmt clippy"

ENV PATH="/home/${USERNAME}/.cargo/bin:${PATH}"

# Clone nginx source code
RUN git clone https://github.com/nginx/nginx.git /opt/nginx \
    && cd /opt/nginx \
    && git checkout release-${NGINX_VERSION}

# Clone nginx-tests
RUN git clone https://github.com/nginx/nginx-tests.git /opt/nginx/tests

ENV NGINX_SOURCE_DIR=/opt/nginx
ENV NGINX_TESTS_DIR=/opt/nginx/tests

# Install pebble
COPY build/get-pebble.sh /build/get-pebble.sh
RUN /build/get-pebble.sh
ENV TEST_NGINX_PEBBLE_BINARY="$PWD/bin/pebble"

WORKDIR /workspaces/nginx-acme
