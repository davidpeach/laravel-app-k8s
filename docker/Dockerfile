FROM php:8.1.12-fpm

ENV DEBIAN_FRONTEND=noninteractive

ARG COMPOSER_VERSION=2.3.5

RUN apt-get update \
    && apt-get install -y curl gnupg tzdata wget gnupg git zip \
    && echo "UTC" > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata

RUN echo $LARAVEL_ENV_ENCRYPTION_KEY

RUN mkdir /data
RUN mkdir /nginx

WORKDIR /data

COPY . .

RUN php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer --version=${COMPOSER_VERSION}

RUN composer install \
        --ignore-platform-reqs \
        --no-interaction \
        --no-scripts \
        --prefer-dist \
        --no-dev

RUN --mount=type=secret,id=LARAVEL_ENV_ENCRYPTION_KEY php artisan env:decrypt --env=production --key=$(cat /etc/secrets/LARAVEL_ENV_ENCRYPTION_KEY) --force
RUN rm .env && mv .env.production .env
