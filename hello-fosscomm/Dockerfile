FROM docker/whalesay

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y lolcat && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

CMD sh -c 'cowsay "Good morning FOSSCOMM!"|/usr/games/lolcat -f'
