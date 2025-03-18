FROM alpine

WORKDIR /app

RUN arch=$(arch | sed s/aarch64/arm64/ | sed s/x86_64/amd64/) && \
	echo ${arch} && \
	# curl -o Assistant http://file.11121314.xyz/Assistant/Assistant_arm64 && \
	chmod +x ./Assistant

EXPOSE 8080

CMD ["./Assistant"]