### Build the image from ubuntu 18.04 LTS
FROM ubuntu:18.04 as builder

### Allow to set-up HTTP(S) proxy using '--build-arg'
ARG http_proxy
ENV http_proxy ${http_proxy}
ARG https_proxy
ENV https_proxy ${https_proxy}
RUN echo 'http_proxy='$http_proxy'\nhttps_proxy='$https_proxy

### Clear local repository & install common tools
RUN apt-get clean \
 && apt-get update && apt-get install -y \
        cpio            \
        curl            \
        git             \
        sudo            \
        unzip           \
        vim             \
        wget

### Configuration settings & options
ENV WORKSPACE           /opt
ENV MOUNT               $WORKSPACE/mnt

WORKDIR $WORKSPACE
RUN : === Download and unpack Intel® Distribution of OpenVINO toolkit package file \
 && curl -O http://registrationcenter-download.intel.com/akdlm/irc_nas/15792/l_openvino_toolkit_p_2019.2.275.tgz \
 && tar zxvf l_openvino_toolkit_*.tgz \
 && : === Install software dependencies and the core components of the toolkit \
 && cd l_openvino_toolkit_*; ./install_openvino_dependencies.sh -y \
 && sed -i 's/^\(ACCEPT_EULA\)=.*$/\1=accept/' silent.cfg \
 && ./install.sh -s silent.cfg \
 && cd ../ ; rm -rf l_openvino_toolkit_*/ \
 && : === Install external software dependencies \
 && cd /opt/intel/openvino/install_dependencies; ./install_openvino_dependencies.sh \
 && : === Set the environment variables \
 && source /opt/intel/openvino/bin/setupvars.sh \
 && : === Configure the Model Optimizer \
 && cd /opt/intel/openvino/deployment_tools/model_optimizer/install_prerequisites; ./install_prerequisites.sh \
 && : === Install OpenCL for Intel GPU \
 && cd /opt/intel/openvino/install_dependencies; mkdir neo; cd neo \
 && wget https://github.com/intel/compute-runtime/releases/download/19.34.13959/intel-gmmlib_19.2.3_amd64.deb \
 && wget https://github.com/intel/compute-runtime/releases/download/19.34.13959/intel-igc-core_1.0.10-2456_amd64.deb \
 && wget https://github.com/intel/compute-runtime/releases/download/19.34.13959/intel-igc-opencl_1.0.10-2456_amd64.deb \
 && wget https://github.com/intel/compute-runtime/releases/download/19.34.13959/intel-opencl_19.34.13959_amd64.deb \
 && wget https://github.com/intel/compute-runtime/releases/download/19.34.13959/intel-ocloc_19.34.13959_amd64.deb \
 && dpkg -i *.deb \
 && apt-get install -y ocl-icd-opencl-dev

### Unset variables
ENV http_proxy ""
ENV https_proxy ""

ENTRYPOINT \
    : === drop the default root privileges depends on the user mounted disk \
 && if [ -d $MOUNT ]; then \
        USERID=`find $MOUNT -maxdepth 0 -printf '%U'`; \
        if [ $USERID -ne 0 ]; then \
            USER=ubuntu; PASSWD=ubuntu; \
            useradd --uid $USERID --create-home --shell /bin/bash $USER; \
            echo "$USER:$PASSWD" | chpasswd; \
            adduser $USER sudo; \
        fi \
    fi \
 && su $USER
