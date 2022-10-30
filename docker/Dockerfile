FROM php:8.1.12-fpm

WORKDIR /var/www/html

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update
RUN apt install -y zip && apt-utils

RUN echo "UTC" > /etc/timezone

ARG PHP_VERSION=8.1
ARG COMPOSER_VERSION=2.3.5

RUN php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer --version=${COMPOSER_VERSION}

COPY . .

RUN composer install --ignore-platform-reqs --no-interaction --no-scripts --prefer-dist --no-dev

EXPOSE 9000
