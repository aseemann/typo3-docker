FROM php:8.1-fpm-alpine as base

FROM base as buildbox

ENV BUILD_DEPS="$PHPIZE_DEPS zlib-dev freetype-dev libjpeg-turbo-dev libmcrypt-dev libpng-dev libxml2-dev libzip-dev ghostscript icu-dev imagemagick-dev"

RUN apk --no-cache add ${BUILD_DEPS}

#FROM buildbox as build-iconv
#
#RUN docker-php-ext-install -j$(nproc) iconv

FROM buildbox as build-gdlib

RUN apk add --no-cache libwebp-dev && docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp \
 && docker-php-ext-install -j$(nproc) gd

FROM buildbox as build-mysql

RUN docker-php-ext-install mysqli pdo pdo_mysql

FROM buildbox as build-zip

RUN docker-php-ext-install zip

FROM buildbox as build-soap

RUN docker-php-ext-install soap

FROM buildbox as build-sockets

RUN docker-php-ext-install sockets

FROM buildbox as build-opcache

RUN docker-php-ext-install opcache

FROM buildbox as build-exif

RUN docker-php-ext-install exif

FROM buildbox as build-intl

RUN docker-php-ext-install intl

FROM buildbox as build-imagick

RUN apk add --no-cache imagemagick && pecl install imagick && docker-php-ext-enable imagick

FROM buildbox as build-acpu

RUN pecl install apcu && docker-php-ext-enable apcu

FROM buildbox as build-redis

RUN pecl install redis && docker-php-ext-enable redis

FROM buildbox as build-igbinary

RUN pecl install igbinary && docker-php-ext-enable igbinary

FROM buildbox as build-xdebug

RUN pecl install xdebug-3.1.6 && docker-php-ext-enable xdebug

FROM base as build-locales

ENV MUSL_LOCALE_DEPS cmake make musl-dev gcc gettext-dev libintl
ENV MUSL_LOCPATH /usr/share/i18n/locales/musl

RUN apk add --no-cache \
    $MUSL_LOCALE_DEPS \
    && wget https://gitlab.com/rilian-la-te/musl-locales/-/archive/master/musl-locales-master.zip \
    && unzip musl-locales-master.zip \
      && cd musl-locales-master \
      && cmake -DLOCALE_PROFILE=OFF -D CMAKE_INSTALL_PREFIX:PATH=/usr . && make && make install \
      && cd .. && rm -r musl-locales-master

FROM base as build-nginx

RUN apk add --no-cache nginx

FROM base

COPY --from=build-nginx /usr/sbin/nginx /usr/sbin/nginx
COPY --from=build-nginx /etc/nginx /etc/nginx

COPY --from=build-gdlib /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-gdlib /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-mysql /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-mysql /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-zip /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-zip /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-soap /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-soap /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-sockets /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-sockets /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-opcache /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-opcache /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-exif /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-exif /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-intl /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-intl /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-acpu /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-acpu /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-imagick /usr/bin/convert /usr/bin
COPY --from=build-imagick /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-imagick /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-redis /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-redis /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-igbinary /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-igbinary /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-xdebug /usr/local/etc/php/conf.d /usr/local/etc/php/conf.d
COPY --from=build-xdebug /usr/local/lib/php/extensions/no-debug-non-zts-20210902/*.so /usr/local/lib/php/extensions/no-debug-non-zts-20210902

COPY --from=build-locales /usr/bin/locale /usr/bin
COPY --from=build-locales /usr/bin/strings /usr/bin

COPY --from=build-locales /usr/share/locale /usr/share/locale
COPY --from=build-locales /usr/share/i18n /usr/share/i18n

RUN set -ex \
    # setup nginx
     && mkdir -p /etc/nginx/conf.d \
                 /etc/nginx/modules.d \
                 /var/log/nginx \
                 /var/cache/nginx \
                 /var/lib/nginx/logs \
                 /var/lib/nginx/tmp \
                 /run/nginx \
     && touch /var/log/nginx/access.log \
              /var/log/nginx/error.log \
     && ln -sf /proc/1/fd/1 /var/log/nginx/access.log \
     && ln -sf /proc/1/fd/1 /var/log/nginx/error.log

RUN runDeps="$(scanelf --needed --nobanner --format '%n#p' --recursive /usr/sbin/nginx /usr/lib/nginx/modules/*.so /usr/local /usr/lib/*.so.*  | tr ',' '\n' | sort -u | awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }')" && \
    apk add -v --no-cache ${runDeps} so:libintl.so.8 && apk add --no-cache curl bash graphicsmagick ghostscript

COPY rootfs/ /
