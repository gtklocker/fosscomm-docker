FROM alpine

RUN apk add --update mpg123 && \
    rm -rf /var/cache/apk/*

COPY music.mp3 /

CMD [ "mpg123", "/music.mp3" ]
