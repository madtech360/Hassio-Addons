# rebased/repackaged base image that only updates existing packages
ARG BASE=mbentley/ubuntu:20.04
FROM ${BASE}

LABEL maintainer="Matt Bentley <mbentley@mbentley.net>"

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
EXPOSE 8088 8043 8843 29810/udp 29811 29812 29813 29814
HEALTHCHECK --start-period=5m CMD /healthcheck.sh
#VOLUME ["/data/tplink/EAPController/data","/data/tplink/EAPController/work","/data/tplink/EAPController/logs"]
ENTRYPOINT ["/entrypoint.sh"]
CMD ["/usr/bin/java","-server","-Xms128m","-Xmx1024m","-XX:MaxHeapFreeRatio=60","-XX:MinHeapFreeRatio=30","-XX:+HeapDumpOnOutOfMemoryError","-XX:HeapDumpPath=/data/tplink/EAPController/logs/java_heapdump.hprof","-Djava.awt.headless=true","-cp","/data/tplink/EAPController/lib/*::/data/tplink/EAPController/properties:","com.tplink.smb.omada.starter.OmadaLinuxMain"]
