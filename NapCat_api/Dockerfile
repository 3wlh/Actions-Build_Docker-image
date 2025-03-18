FROM alpine:edge

WORKDIR /app

RUN arch=$(arch | sed s/aarch64/arm64/ | sed s/x86_64/amd64/) && \
	wget -O napcat_api http://file.11121314.xyz/NapCat/${arch}/napcat_api && \
	chmod +x ./napcat_api

EXPOSE 8080

CMD ["./napcat_api"]