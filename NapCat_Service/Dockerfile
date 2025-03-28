FROM alpine:edge

WORKDIR /app

RUN arch=$(arch | sed s/aarch64/arm64/ | sed s/x86_64/amd64/) && \
	wget -O napcat_api http://file.11121314.xyz/NapCat_Service/${arch}/napcat_service && \
	chmod +x ./napcat_service

EXPOSE 8080

CMD ["./napcat_service"]