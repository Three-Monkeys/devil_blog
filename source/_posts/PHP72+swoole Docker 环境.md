---
title: PHP72+swoole Docker 环境
categories:
- 容器
tags:
- Docker
- PHP
---

## Dockerfile
```shell
FROM php:7.2-fpm

LABEL user = DevilCcx

RUN apt-get update && \
    apt-get install -y --allow-unauthenticated --no-install-recommends \
        libmemcached-dev \
        libz-dev \
        libpq-dev \
        libjpeg-dev \
        libpng-dev \
        libfreetype6-dev \
        libssl-dev \
        libmcrypt-dev \
        openssh-server \
        libmagickwand-dev \
        git \
        cron \
        nano \
        libxml2-dev


RUN apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-install -j$(nproc) iconv \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd

# 安装一些拓展（soap、exif、mcrypt、pcntl、pdo_mysql、bcmath、imagick、gd ）
RUN docker-php-ext-install soap exif pcntl pdo_mysql bcmath \
    && pecl install mcrypt-1.0.2 imagick swoole \
    && docker-php-ext-enable mcrypt imagick swoole

#####################################
# Composer:
#####################################

# Install composer and add its bin to the PATH.
RUN curl -s http://getcomposer.org/installer | php && \
    echo "export PATH=${PATH}:/var/www/vendor/bin" >> ~/.bashrc && \
    mv composer.phar /usr/local/bin/composer && \
    composer config -g repo.packagist composer https://packagist.laravel-china.org

EXPOSE 9000

CMD ["php-fpm"]
```

## docker部署
* 启动php-fpm容器
```
# 构建镜像
docker build -t php:v1 .
# 运行容器
docker run -d -p 9000:9000 -v 项目路径:/var/www/html php:v1
```
* 配合nginx
```
location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME /var/www/html/public$fastcgi_script_name;
        include fastcgi_params;
    }
```
