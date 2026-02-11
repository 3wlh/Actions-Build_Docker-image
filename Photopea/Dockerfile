FROM alpine AS builder

WORKDIR /app/photopea

RUN apk update && apk upgrade

RUN apk add git

#RUN git clone --progress --verbose https://gitflic.ru/project/photopea-v2/photopea-v-2.git . && \
#	rm -fr _vendor Updater.py README.md .git
	
RUN wget -O photopea.tar.gz https://github.com/3wlh/Actions-Build_Docker-image/releases/download/photopea/www.photopea.com.tar.gz && \
	mkdir -p www.photopea.com && \
	tar -zxf photopea.tar.gz -C www.photopea.com && rm -fr www.photopea.com.tar.gz


RUN wget -O photopea.tar.gz https://github.com/3wlh/Actions-Build_Docker-image/releases/download/photopea/photopea.tar.gz && \
	tar -zxf photopea.tar.gz -C www.photopea.com/rsrc/fonts/ && rm -fr photopea.tar.gz
	
RUN arch=$(arch | sed s/aarch64/arm64/ | sed s/x86_64/amd64/) && \
	wget -O web https://github.com/3wlh/Actions-Build_Docker-image/releases/download/photopea/photopea_${arch} && \
	chmod +x ./web

FROM alpine

COPY --from=builder  /app/photopea/ /app/

WORKDIR /app

EXPOSE 8887

ENTRYPOINT ["./web", "-p", "8887", "-d", "photopea"]