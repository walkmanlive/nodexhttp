FROM ubuntu:22.04 AS builder

WORKDIR /app

RUN apt-get update; \
    apt-get install -y wget unzip; \
    wget -O supercronic https://github.com/aptible/supercronic/releases/latest/download/supercronic-linux-amd64; \
    chmod +x supercronic
    apt-get clean; \
    rm -rf /var/lib/apt/lists/*
############################################################

FROM ubuntu:22.04

LABEL org.opencontainers.image.source="https://github.com/walkmanlive/nodexhttp"

ENV TZ=Asia/Shanghai \
    UUID=7ca0700b-98f5-4b44-ab72-cace661a4357 \
    DOMAIN=hopto-xp.hf.space

COPY entrypoint.sh /entrypoint.sh
COPY app /app

RUN export DEBIAN_FRONTEND=noninteractive; \
    apt-get update; \
    apt-get install -y tzdata curl ca-certificates wget vim net-tools supervisor unzip iputils-ping git iproute2 --no-install-recommends; \
    apt-get clean; \
    rm -rf /var/lib/apt/lists/*; \
    chmod +x /entrypoint.sh; \
    chmod -R 755 /app; \
    useradd -u 1000 -g 0 -m -s /bin/bash user; \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime; \
    echo $TZ > /etc/timezone

COPY --from=builder /app/supercronic /usr/local/bin/supercronic

EXPOSE 7860

ENTRYPOINT ["/entrypoint.sh"]
CMD ["supervisord", "-c", "/app/supervisor/supervisord.conf"]
