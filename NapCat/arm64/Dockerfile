FROM alpine

WORKDIR /app

ADD http://file.11121314.xyz/Assistant/Assistant_arm64 /app/Assistant

RUN chmod +x ./Assistant

EXPOSE 8080

CMD ["./Assistant"]