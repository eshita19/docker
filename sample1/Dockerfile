#Alpine image
# Command to create image: docker build -t myapp:1.0 . --build-arg LATEST_VERSION=latest

#ARG creates variable during docker image built
ARG LATEST_VERSION=latest
#Downloads an existing image and use it as base image
FROM alpine:${LATEST_VERSION}
#ARG LATEST_VERSION
FROM mysql:${LATEST_VERSION}
FROM nginx:${LATEST_VERSION}
ARG LATEST_VERSION

#Runs command during image built
RUN echo 'Version is' ${LATEST_VERSION}
RUN mkdir -p /opt/cisco

#Runs command during container creation
CMD ls -lrt
CMD echo "Hello....."

#Metadata 
LABEL author="eshita"

COPY ./Dockerfile /

VOLUME mysql-db:/data

ENV name eshita
ENTRYPOINT echo $name