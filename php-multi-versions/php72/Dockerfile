FROM php:7.2-fpm

# 替换为阿里云 Debian 源
RUN sed -i 's|http://deb.debian.org/debian|http://mirrors.aliyun.com/debian|g' /etc/apt/sources.list \
    && sed -i 's|http://security.debian.org/debian-security|http://mirrors.aliyun.com/debian-security|g' /etc/apt/sources.list \
    && apt-get update

# 安装 Composer
RUN apt-get install -y curl unzip \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && composer config -g repo.packagist composer https://packagist.phpcomposer.com

RUN apt-get update && apt-get install -y \
    libpng-dev libjpeg-dev libfreetype6-dev \
    && docker-php-ext-install pdo pdo_mysql gd zip pcntl bcmath \
    && pecl install redis-5.3.7 \
    && docker-php-ext-enable redis

# 安装 procps（提供 `ps` 命令）
RUN apt-get update && apt-get install -y procps
# 设置 PHP 内存限制
RUN echo "memory_limit=512M" > /usr/local/etc/php/conf.d/memory_limit.ini
# 安装 vim
RUN apt update && apt install -y vim

# 安装 cron
RUN apt update && apt install -y cron

# 复制 crontab 任务文件
COPY crontab /etc/cron.d/my-cron-job
RUN chmod 0644 /etc/cron.d/my-cron-job
RUN crontab /etc/cron.d/my-cron-job

# 确保 `cron` 服务运行
CMD service cron start && php-fpm

WORKDIR /var/www/html
