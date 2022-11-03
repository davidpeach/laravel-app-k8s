FROM php:8.1.12-fpm

ENV DEBIAN_FRONTEND=noninteractive

ARG PHP_VERSION=8.1
ARG COMPOSER_VERSION=2.3.5

RUN apt-get update \
    && apt-get install -y curl gnupg tzdata wget gnupg git zip \
    && echo "UTC" > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata

ARG USER=www
ARG UID=1000

RUN php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer --version=${COMPOSER_VERSION}

# RUN groupadd -g $UID $USER
# RUN useradd -u $UID -ms /bin/bash -g $USER $USER

RUN mkdir /data
# RUN chown ${USER}:$USER /data

RUN mkdir /nginx
# RUN chown ${USER}:$USER /nginx


# WORKDIR /data
# RUN sed -i "1s/.*/user $USER;/" /etc/nginx/nginx.conf && \
#     chown --recursive $USER:$USER /var/www

# Change current user to dynamic user for app setup
# USER $USER

# COPY --chown=${USER}:${USER} database/ database/
# COPY --chown=${USER}:${USER} composer.json composer.lock ./

RUN composer install \
        --ignore-platform-reqs \
        --no-interaction \
        --no-scripts \
        --prefer-dist \
        --no-dev
RUN ls -la
RUN php artisan env:decrypt --env=production --key=${LARAVEL_ENV_ENCRYPTION_KEY} --force
RUN rm .env && mv .env.production .env

WORKDIR /data

COPY . .

# Add general application code last to preserve cache as much as possible
# COPY --chown=${USER}:${USER} . .
# COPY --chown=${USER}:${USER} ./docker/nginx/* /nginx/

# RUN rm -rf public
# COPY --chown=${USER}:${USER} public/index.php public/index.php

# Change current user back to root
# USER root
# CMD ["php8.1-fpm"]
# CMD ["/usr/bin/supervisord", "-n"] 
