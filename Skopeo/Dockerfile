FROM ubuntu

RUN apt update && \
    apt install -y --no-install-recommends jq wget skopeo && \
    rm -rf /var/lib/apt/lists/*
    
RUN skopeo --version
    
ENTRYPOINT ["/bin/bash"]