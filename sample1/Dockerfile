#Alpine image
#docker build -t myapp:1.0 . --build-arg ALPINE_VERSION=latest

ARG ALPINE_VERSION=3.2
FROM alpine:${ALPINE_VERSION}
ARG ALPINE_VERSION
RUN echo 'Version is' ${ALPINE_VERSION}
