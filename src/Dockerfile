# 基于 php-fpm 镜像
# FROM python:3.7.4-slim-stretch
FROM php:7.0-fpm

RUN sed -i "s@http://deb.debian.org@http://mirrors.aliyun.com@g" /etc/apt/sources.list && \
    rm -Rf /var/lib/apt/lists/*

# 安装 nginx，并根据需要安装PHP扩展
RUN apt-get update && apt-get install -y \
        nginx \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
        procps \
    && docker-php-ext-install -j$(nproc) iconv mcrypt \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd

COPY scripts/start.sh /start.sh
RUN chmod 755 /start.sh

# EXPOSE 8080
EXPOSE 8080

CMD ["/start.sh"]