### Build the image from ubuntu 16.04 LTS
FROM ubuntu:16.04

### Allow to set-up HTTP(S) proxy using '--build-arg'
ARG http_proxy
ENV http_proxy ${http_proxy}
ARG https_proxy
ENV https_proxy ${https_proxy}
RUN echo 'http_proxy='$http_proxy'\nhttps_proxy='$https_proxy

### Clear local repository & install common tools
RUN apt-get clean \
 && apt-get update && apt-get install -y \
        curl            \
        git             \
        unzip           \
        vim             \
        wget

### Configuration settings & options
ENV WORKSPACE           /opt
ENV MOUNT               $WORKSPACE/mnt

### Builder for native IoTivity
WORKDIR $MOUNT
RUN : === install build dependencies \
          https://wiki.iotivity.org/build_iotivity_with_ubuntu_build_machine \
 && apt-get update && apt-get install -y \
        build-essential git scons libtool autoconf valgrind doxygen wget unzip \
        libboost-dev libboost-program-options-dev libboost-thread-dev \
        uuid-dev libexpat1-dev libglib2.0-dev libsqlite3-dev libcurl4-gnutls-dev

### Unset variables
ENV http_proxy ""
ENV https_proxy ""

### Mount points for holding external volumes
VOLUME $MOUNT
