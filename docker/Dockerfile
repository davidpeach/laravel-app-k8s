FROM php:8.1.12-fpm


ENV DEBIAN_FRONTEND=noninteractive

ARG PHP_VERSION=8.1
ARG COMPOSER_VERSION=2.3.5

RUN apt-get update \
    && apt-get install -y curl gnupg tzdata wget gnupg git zip \
    && echo "UTC" > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata

# RUN apt-get install -y --no-install-recommends \
#         gosu \
#         git \
#         zip \
#         unzip \
#         nginx \
#         supervisor \
#         mysql-client \
#         pdftk-java \
#         software-properties-common && \
#     add-apt-repository -y ppa:ondrej/php && \
# RUN apt-get update && \
#     apt-get install -y php${PHP_VERSION}-fpm php${PHP_VERSION}-cli php${PHP_VERSION}-gd php${PHP_VERSION}-mysql \
#          php${PHP_VERSION}-pgsql php${PHP_VERSION}-imap php-memcached php${PHP_VERSION}-mbstring php${PHP_VERSION}-xml \
#          php${PHP_VERSION}-curl php${PHP_VERSION}-sqlite3 php${PHP_VERSION}-xdebug php${PHP_VERSION}-soap \
#          php${PHP_VERSION}-zip php${PHP_VERSION}-imagick php${PHP_VERSION}-dom php${PHP_VERSION}-bcmath

# COPY docker/default.nginx /etc/nginx/sites-available/default
# COPY docker/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
# COPY docker/php-fpm.conf /etc/php/${PHP_VERSION}/fpm/php-fpm.conf
# COPY docker/www-overrides.conf /etc/php/${PHP_VERSION}/fpm/pool.d/www2.conf

ARG USER=www
ARG UID=1000

RUN php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer --version=${COMPOSER_VERSION}

RUN groupadd -g $UID $USER
RUN useradd -u $UID -ms /bin/bash -g $USER $USER
RUN mkdir /data
RUN chown ${USER}:$USER /data
WORKDIR /data
# RUN sed -i "1s/.*/user $USER;/" /etc/nginx/nginx.conf && \
#     chown --recursive $USER:$USER /var/www

# Change current user to dynamic user for app setup
USER $USER

COPY --chown=${USER}:${USER} database/ database/
COPY --chown=${USER}:${USER} composer.json composer.lock ./

RUN composer install \
        --ignore-platform-reqs \
        --no-interaction \
        --no-scripts \
        --prefer-dist \
        --no-dev

# Add general application code last to preserve cache as much as possible
COPY --chown=${USER}:${USER} . .
RUN rm -rf public
COPY --chown=${USER}:${USER} public/index.php public/index.php

# Change current user back to root
# USER root
# CMD ["php8.1-fpm"]
# CMD ["/usr/bin/supervisord", "-n"] 
