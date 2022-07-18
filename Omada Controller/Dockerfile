# rebased/repackaged base image that only updates existing packages
ARG BASE=mbentley/ubuntu:20.04
FROM ${BASE}

LABEL maintainer="Matt Bentley <mbentley@mbentley.net>"

HEALTHCHECK CMD wget --quiet --tries=1 --no-check-certificate http://127.0.0.1:8088 || exit 1

ARG OMADA_VER=5.3.1
ARG OMADA_TAR="Omada_SDN_Controller_v${OMADA_VER}_Linux_x64.tar.gz"
ARG OMADA_URL="https://static.tp-link.com/upload/software/2022/202205/20220507/${OMADA_TAR}"
# valid values: amd64 (default) | arm64 | armv7l
ARG ARCH=arm64

COPY install.sh healthcheck.sh entrypoint.sh /

RUN chmod +x /*.sh

# install omada controller (instructions taken from install.sh); then create a user & group and set the appropriate file system permissions
RUN /install.sh && rm /install.sh

# patch log4j vulnerability
#COPY log4j_patch.sh /log4j_patch.sh
#RUN /log4j_patch.sh

COPY log4j2.properties /data/tplink/EAPController/properties/

WORKDIR /data/tplink/EAPController/lib
EXPOSE 8043/tcp 8088/tcp 27001/udp 27002/tcp 29810/tcp 29810/udp 29811/tcp 29811/udp 29812/tcp 29812/udp 29813/tcp 29813/udp 29814/tcp 29814/udp
HEALTHCHECK --start-period=5m CMD /healthcheck.sh
VOLUME ["/data/tplink/EAPController/data","/data/tplink/EAPController/work","/data/tplink/EAPController/logs"]
ENTRYPOINT ["/entrypoint.sh"]
CMD ["/usr/bin/java","-server","-Xms128m","-Xmx1024m","-XX:MaxHeapFreeRatio=60","-XX:MinHeapFreeRatio=30","-XX:+HeapDumpOnOutOfMemoryError","-XX:HeapDumpPath=/data/tplink/EAPController/logs/java_heapdump.hprof","-Djava.awt.headless=true","-cp","/data/tplink/EAPController/lib/*::/data/tplink/EAPController/properties:","com.tplink.smb.omada.starter.OmadaLinuxMain"]
