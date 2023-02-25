FROM debian:bullseye-20230208 as builder

ARG UNBOUND_VERSION=1.17.1

WORKDIR /build

RUN apt-get update
RUN apt-get install -y \
  curl \
  unzip \
  build-essential \
  libexpat1-dev \
  libssl-dev \
  flex \
  bison
RUN curl -SsL -o unbound.zip https://github.com/NLnetLabs/unbound/archive/refs/tags/release-${UNBOUND_VERSION}.zip
RUN unzip unbound.zip

WORKDIR /build/unbound-release-${UNBOUND_VERSION}

RUN ./configure -h
RUN ./configure && make && make install

FROM debian:bullseye-20230208-slim

WORKDIR /app

RUN \
  apt-get update \
  && \
  apt-get install -y \
    libevent-dev \
    libssl-dev \
  && \
  rm -rf /var/lib/apt/lists/* \
  && \
  mkdir -p /usr/local/etc/unbound

COPY --from=builder /usr/local/sbin/unbound* /app

VOLUME ["/usr/local/etc/unbound"]

EXPOSE 53/udp 53/tcp

ENTRYPOINT [ "/app/unbound", "-d" ]