FROM docker.io/bitnami/minideb:buster
LABEL maintainer "Bitnami <containers@bitnami.com>"

ENV HOME="/" \
    OS_FLAVOUR="debian-10" \
    OS_NAME="linux"

COPY prebuildfs /
# Install required system packages and dependencies
RUN install_packages acl ca-certificates curl gzip jq libc6 procps tar wget && \
    if [ $(uname -m) = "x86_64" ]; then export OS_ARCH=amd64; elif [ $(uname -m) = "aarch64" ]; then export OS_ARCH=arm64; else exit 1; fi && \
    mkdir -p /opt/bitnami/common/bin && \
    wget https://github.com/bitnami/wait-for-port/releases/download/v1.0.1/wait-for-port-linux-${OS_ARCH}.tar.gz -O /tmp/wait-for-port-linux-${OS_ARCH}.tar.gz && \
    tar -xzf /tmp/wait-for-port-linux-${OS_ARCH}.tar.gz -C /opt/bitnami/common/bin && \
    mv /opt/bitnami/common/bin/wait-for-port-linux-${OS_ARCH} /opt/bitnami/common/bin/wait-for-port && \
    rm /tmp/wait-for-port-linux-${OS_ARCH}.tar.gz && \
    wget https://github.com/tianon/gosu/releases/download/1.14/gosu-${OS_ARCH} -O /opt/bitnami/common/bin/gosu && \
    chmod +x /opt/bitnami/common/bin/gosu && \
    mkdir -p /opt/bitnami/minio/bin && \
    wget https://dl.min.io/server/minio/release/linux-${OS_ARCH}/minio -O /opt/bitnami/minio/bin/minio && \
    chmod +x /opt/bitnami/minio/bin/minio && \
    mkdir -p /opt/bitnami/minio-client/bin && \
    wget https://dl.min.io/client/mc/release/linux-${OS_ARCH}/mc -O /opt/bitnami/minio-client/bin/mc && \
    chmod +x /opt/bitnami/minio-client/bin/mc

# RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "wait-for-port" "1.0.1-10" --checksum 35c818ba3f4b5aae905959bc7d3a5e81fc63786e3c662b604612c0aa7fcda8fd
# RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "minio-client" "2022.4.16-0" --checksum aee74f7696df369551a18e2a8de12f1876d1bc0103bb24e2b110da8e94d31885
# RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "minio" "2022.4.26-0" --checksum a3f71486d46499f2fff096c9d971e4ff2e3e00b522855aae4eaf5e518c355fd7
# RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "gosu" "1.14.0-7" --checksum d6280b6f647a62bf6edc74dc8e526bfff63ddd8067dcb8540843f47203d9ccf1

RUN apt-get update && apt-get upgrade -y && \
    rm -r /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami

COPY rootfs /
RUN /opt/bitnami/scripts/minio-client/postunpack.sh
RUN /opt/bitnami/scripts/minio/postunpack.sh
ENV APP_VERSION="2022.4.26" \
    BITNAMI_APP_NAME="minio" \
    PATH="/opt/bitnami/common/bin:/opt/bitnami/minio-client/bin:/opt/bitnami/minio/bin:$PATH"

VOLUME [ "/certs", "/data" ]

EXPOSE 9000 9001

WORKDIR /opt/bitnami/minio-client
USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/minio/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/minio/run.sh" ]
