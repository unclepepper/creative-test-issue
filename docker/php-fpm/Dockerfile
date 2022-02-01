FROM composer:latest as composer
FROM php:8.0.0-fpm-alpine as php

RUN set -xe \
        && apk add --no-cache \
           shadow \
           libzip-dev \
           libintl \
           icu \
           icu-dev \
           curl \
           libmcrypt \
           libmcrypt-dev \
           libxml2-dev \
           freetype \
           freetype-dev \
           libpng \
           libpng-dev \
           libjpeg-turbo \
           libjpeg-turbo-dev \
           postgresql-dev \
           pcre-dev \
           git \
           g++ \
           make \
           autoconf \
           openssh \
           util-linux-dev \
           libuuid \
           sqlite-dev \
           libxslt-dev

RUN docker-php-ext-install \
    zip \
    iconv \
    soap \
    sockets \
    intl \
    pdo_mysql \
    pdo_pgsql \
    exif \
    pcntl \
    xsl

RUN pecl update-channels && pecl install xdebug && \
    docker-php-ext-enable xdebug \
    && echo  xdebug.mode=debug >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

ENV XDEBUG_CONFIG="client_host=host.docker.internal client_port=9001 start_with_request=yes"
    

COPY --from=composer /usr/bin/composer /usr/local/bin/composer
RUN /usr/local/bin/composer self-update


EXPOSE 9000
WORKDIR /var/www/
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV COMPOSER_MEMORY_LIMIT=-1

ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

RUN usermod -u ${PUID} www-data && groupmod -g ${PGID} www-data
RUN mkdir -p /var/www/ && chown -R www-data:www-data /var/www/

RUN echo 'memory_limit=1536M' > /usr/local/etc/php/conf.d/memory.ini
RUN echo 'upload_max_filesize = 10M' > /usr/local/etc/php/conf.d/upload-size.ini
RUN echo 'post_max_size = 10M' >> /usr/local/etc/php/conf.d/upload-size.ini

USER ${PUID}:${PGID}
