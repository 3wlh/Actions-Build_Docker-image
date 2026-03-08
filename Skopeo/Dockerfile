FROM ubuntu

RUN apt update && \
    apt install -y --no-install-recommends \
    jq wget ca-certificates && \
    rm -rf /var/lib/apt/lists/*

ENV ARCH=amd64 \
    VERSION=v1.22.0 \
    BASE_URL=https://github.com/lework/skopeo-binary/releases/download
    
RUN wget -O /usr/bin/skopeo ${BASE_URL}/${VERSION}/skopeo-linux-${ARCH} &&\
	chmod +x /usr/bin/skopeo &&\
	skopeo --version
    
ENTRYPOINT ["/bin/bash"]