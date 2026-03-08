FROM ubuntu

RUN apt update && \
    apt install -y --no-install-recommends jq wget && \
    rm -rf /var/lib/apt/lists/*

ENV ARCH=amd64 \
    VERSION=v1.22.0 \
    DOWNLOAD_URL=https://github.com/lework/skopeo-binary/releases/download/${VERSION}/skopeo-linux-${ARCH}

RUN wget -O /usr/bin/skopeo ${DOWNLOAD_URL} && \
    chmod +x /usr/bin/skopeo && \
    skopeo --version

ENTRYPOINT ["/bin/bash"]