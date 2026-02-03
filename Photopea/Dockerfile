FROM alpine

WORKDIR /var/run/photopea

RUN apk update && apk upgrade

RUN apk add git python3

RUN git clone --progress --verbose https://gitflic.ru/project/photopea-v2/photopea-v-2.git . && \
	rm -fr _vendor Updater.py README.md .git www.photopea.com\rsrc\fonts\fonts.png \
	wget -qO - http://script.11121314.xyz/Docker/photopea/photopea.sh | sh

RUN wget -O photopea.tar.gz https://github.com/3wlh/Actions-Build_Docker-image/releases/download/photopea/photopea.tar.gz && \
	tar -zxf photopea.tar.gz -C www.photopea.com/rsrc/fonts/ && rm -fr photopea.tar.gz && \
	sed -i '/var FNTS = {/,/};/d' www.photopea.com/code/dbs/DBS1751979998.js && \
	cat www.photopea.com/rsrc/fonts/FNTS.js >> www.photopea.com/code/dbs/DBS1751979998.js && \
	mv -f www.photopea.com/rsrc/fonts/22.js www.photopea.com/code/lang/

EXPOSE 8887

ENTRYPOINT ["python3", "-m", "http.server", "--directory", "www.photopea.com", "8887"]