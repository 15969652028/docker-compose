FROM redis:latest

# 安装 procps（提供 `ps` 命令）
RUN apt-get update && apt-get install -y procps

# 更新包管理器并安装 redis-cli
RUN apt-get update && apt-get install -y redis-tools

CMD ["redis-server"]