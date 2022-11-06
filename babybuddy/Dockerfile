ARG BUILD_FROM=ghcr.io/hassio-addons/base/amd64:10.0.0
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

VOLUME ["/run"]

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN echo 'http://dl-cdn.alpinelinux.org/alpine/v3.14/main' >> /etc/apk/repositories
RUN apk update
# hadolint ignore=DL3003
RUN \
  echo "**** install build packages ****" && \
  #apk add --no-cache --virtual=build-dependencies \
  #  build-base \
  apk add --no-cache --virtual=build-dependencies gcc
RUN apk add --no-cache --virtual=build-dependencies curl 
RUN apk add --no-cache --virtual=build-dependencies jpeg-dev
RUN apk add --no-cache --virtual=build-dependencies libffi-dev
RUN apk add --no-cache --virtual=build-dependencies libxml2-dev
RUN apk add --no-cache --virtual=build-dependencies libxslt-dev
RUN apk add --no-cache --virtual=build-dependencies postgresql-dev
RUN apk add --no-cache --virtual=build-dependencies python3-dev
RUN apk add --no-cache --virtual=build-dependencies zlib-dev

# hadolint ignore=DL3003
RUN \
  echo "**** install runtime packages ****" && \
  apk add --no-cache \
    jpeg \
    libffi \
    libpq \
    libxml2 \
    libxslt \
    py3-mysqlclient \
    py3-pip \
    python3 \
    nginx

RUN \
  echo "**** downloading babybuddy ****" && \
  curl -o \
    /tmp/babybuddy.tar.gz -L \
    "https://github.com/babybuddy/babybuddy/archive/refs/tags/v1.13.2.tar.gz" && \
  mkdir -p /app/babybuddy && \
  tar xf \
    /tmp/babybuddy.tar.gz -C \
    /app/babybuddy --strip-components=1 

RUN \
  echo "**** installing babybuddy ****" && \
  cd /app/babybuddy && \
  pip3 install -U --no-cache-dir \
    pip && \
  pip install -U --ignore-installed -r requirements.txt

RUN \
  echo "**** cleanup ****" && \
  apk del --purge \
    build-dependencies && \
  rm -rf \
    /etc/nginx \
    /opt/build \
    /opt/index.html \
    /opt/package.sh \
    /opt/src \
    /opt/static \
    /opt/stylesheets \
    /opt/.[!.]* \
    /opt/*.md \
    /opt/nodemon.json \
    /opt/kustomization.yaml \
    /opt/kubernetes \
    /opt/docker \
    /opt/docs \
    /root/.cache \
    /root/.config \
    /root/.npmrc \
    /root/.node-gyp \
    /root/.npm \
    /tmp/.[!.]* \
    /tmp/* \
    /usr/lib/node_modules \
    /usr/local/share/.cache

COPY rootfs /

# Build arguments
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION}
