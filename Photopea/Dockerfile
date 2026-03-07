FROM alpine AS builder

WORKDIR /app/photopea

RUN apk update && apk upgrade

RUN apk add git

ENV \
	DOWNLOAD_URL="https://github.com/3wlh/Actions-Docker/releases/download/Files-Photopea" \
	GIT_URL="https://gitflic.ru/project/photopea-v2/photopea-v-2.git"

#RUN git clone --progress --verbose ${GIT_URL) . && \
#	rm -fr _vendor Updater.py README.md .git
	
RUN wget -O www.photopea.com.tar.gz \
	${DOWNLOAD_URL}/photopea-2.7.tar.gz && \
	mkdir -p www.photopea.com && \
	tar -zxf www.photopea.com.tar.gz -C www.photopea.com && rm -fr www.photopea.com.tar.gz


RUN wget -O fonts.tar.gz ${DOWNLOAD_URL}/fonts.tar.gz && \
	tar -zxf fonts.tar.gz -C www.photopea.com/rsrc/fonts/ && rm -fr fonts.tar.gz
	
RUN arch=$(arch | sed s/aarch64/arm64/ | sed s/x86_64/amd64/) && \
	wget -O service ${DOWNLOAD_URL}/photopea_${arch} && \
	chmod +x ./service

FROM alpine

COPY --from=builder  /app/photopea/ /app/

WORKDIR /app

EXPOSE 8887

ENTRYPOINT ["./service", "-p", "8887", "-d", "photopea"]