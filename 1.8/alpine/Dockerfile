FROM alpine:3.5

RUN addgroup app && adduser -h /home/app -s /bin/sh -D -G app app && \
    apk update && \
    apk upgrade && \
    apk add --no-cache \
    gcc \
    g++ \
    git \
    openssl \
    sqlite-dev \
    ca-certificates

ENV GOLANG_VERSION 1.8rc1
ENV GOLANG_SRC_URL https://golang.org/dl/go$GOLANG_VERSION.src.tar.gz
ENV GOLANG_SRC_SHA256 aa25758b754369018c507bbf646dda7839a41ffe4fd85a014c9704d7d8720dda

# https://golang.org/issue/14851
COPY no-pic.patch /

RUN set -ex \
	&& apk add --no-cache --virtual .build-deps \
		bash \
		musl-dev \
		go \
	\
	&& export GOROOT_BOOTSTRAP="$(go env GOROOT)" \
	\
	&& wget -q "$GOLANG_SRC_URL" -O golang.tar.gz \
	&& echo "$GOLANG_SRC_SHA256  golang.tar.gz" | sha256sum -c - \
	&& tar -C /usr/local -xzf golang.tar.gz \
	&& rm golang.tar.gz \
	&& cd /usr/local/go/src \
	&& patch -p2 -i /no-pic.patch \
	&& ./make.bash \
	\
	&& rm -rf /*.patch \
	&& apk del .build-deps

# ENV GOPATH /go
ENV GOPATH /home/app/go
ENV PATH $GOPATH/bin:/usr/local/go/bin:$PATH

RUN mkdir -p "$GOPATH/src" "$GOPATH/bin" && chmod -R 777 "$GOPATH"

COPY go-wrapper /usr/local/bin/

WORKDIR $GOPATH
ENV HOME=/home/app
USER app
