FROM ubuntu:22.04

WORKDIR /var/www/html

ENV DEBIAN_FRONTEND=noninteractive

ARG PHP_VERSION=8.1
ARG COMPOSER_VERSION=2.3.5

RUN apt-get update \
    && apt-get install -y curl gnupg tzdata wget gnupg \
    && echo "UTC" > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata

RUN apt-get install -y --no-install-recommends \
        gosu \
        git \
        zip \
        unzip \
        nginx \
        supervisor \
        mysql-client \
        pdftk-java \
        software-properties-common && \
    add-apt-repository -y ppa:ondrej/php && \
    apt-get update && \
    apt-get install -y php${PHP_VERSION}-fpm php${PHP_VERSION}-cli php${PHP_VERSION}-gd php${PHP_VERSION}-mysql \
         php${PHP_VERSION}-pgsql php${PHP_VERSION}-imap php-memcached php${PHP_VERSION}-mbstring php${PHP_VERSION}-xml \
         php${PHP_VERSION}-curl php${PHP_VERSION}-sqlite3 php${PHP_VERSION}-xdebug php${PHP_VERSION}-soap \
         php${PHP_VERSION}-zip php${PHP_VERSION}-imagick php${PHP_VERSION}-dom php${PHP_VERSION}-bcmath

COPY docker/default.nginx /etc/nginx/sites-available/default
COPY docker/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

RUN php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer --version=${COMPOSER_VERSION}

COPY . .

RUN composer install --ignore-platform-reqs --no-interaction --no-scripts --prefer-dist --no-dev

EXPOSE 80

CMD ["usr/bin/supervisord", "-n"] 
