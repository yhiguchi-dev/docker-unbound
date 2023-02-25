FROM debian:bullseye-20230208 as builder

ARG UNBOUND_VERSION=1.17.1

WORKDIR /build

RUN apt-get update
RUN apt-get install --no-install-recommends -y \
  curl=7.74.0-1.3+deb11u5 \
  ca-certificates=20210119 \
  unzip=6.0-26+deb11u1 \
  build-essential=12.9 \
  libexpat1-dev=2.2.10-2+deb11u5 \
  libssl-dev=1.1.1n-0+deb11u4 \
  flex=2.6.4-8 \
  bison=2:3.7.5+dfsg-1
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
  apt-get install --no-install-recommends -y \
    libssl-dev=1.1.1n-0+deb11u4 \
    # CVE-2023-0361
    libgnutls30=3.7.1-5+deb11u3 \
  && \
  rm -rf /var/lib/apt/lists/* \
  && \
  mkdir -p /usr/local/etc/unbound

COPY --from=builder /usr/local/sbin/unbound* /app

VOLUME ["/usr/local/etc/unbound"]

EXPOSE 53/udp 53/tcp

ENTRYPOINT [ "/app/unbound", "-d" ]