FROM ubuntu:22.04 AS builder
WORKDIR /app
RUN apt-get update; \
    apt-get install -y wget ca-certificates --no-install-recommends; \
    # 仅保留合法定时任务工具 supercronic，彻底删除 ttyd 网页终端二进制
    wget -O supercronic https://github.com/aptible/supercronic/releases/latest/download/supercronic-linux-amd64; \
    chmod +x supercronic; \
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
    # 移除 openssh-server、telnet 等高危终端/网络服务，仅保留业务必要工具
    apt-get install -y tzdata curl ca-certificates wget vim net-tools supervisor unzip iputils-ping git iproute2 --no-install-recommends; \
    apt-get clean; \
    rm -rf /var/lib/apt/lists/*; \
    chmod +x /entrypoint.sh; \
    chmod -R 755 /app; \
    useradd -u 1000 -g 0 -m -s /bin/bash user; \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime; \
    echo $TZ > /etc/timezone

# 仅复制合规的定时任务工具，彻底移除 ttyd 终端程序
COPY --from=builder /app/supercronic /usr/local/bin/supercronic

EXPOSE 7860

ENTRYPOINT ["/entrypoint.sh"]
CMD ["supervisord", "-c", "/app/supervisor/supervisord.conf"]
