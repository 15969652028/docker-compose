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

WORKDIR /var/www/html
