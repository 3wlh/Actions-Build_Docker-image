FROM alpine AS builder

WORKDIR /app/photopea

RUN apk update && apk upgrade

RUN apk add git

RUN git clone --progress --verbose https://gitflic.ru/project/photopea-v2/photopea-v-2.git . && \
	rm -fr _vendor Updater.py README.md .git && \
	rm -fr www.photopea.com/rsrc/fonts/fonts.png www.photopea.com/img/*

RUN wget -O photopea.tar.gz https://github.com/3wlh/Actions-Build_Docker-image/releases/download/photopea/photopea.tar.gz && \
	tar -zxf photopea.tar.gz -C www.photopea.com/rsrc/fonts/ && rm -fr photopea.tar.gz
	
RUN wget -qO - http://script.11121314.xyz/Docker/photopea/photopea.sh | sh

FROM python:alpine

COPY --from=builder  /app/photopea/ /app/

WORKDIR /app

EXPOSE 8887

ENTRYPOINT ["python3", "-m", "http.server", "--directory", "photopea", "8887"]