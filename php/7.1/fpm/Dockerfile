FROM php:7.1-fpm

MAINTAINER Mriyam Tamuli <mbtamuli@gmail.com>

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        curl \
        libc-client-dev \
        libedit-dev \
        libkrb5-dev \
        libmcrypt-dev \
        libreadline-dev \
        librecode-dev \
        # libmemcached-dev \
        libpq-dev \
        libssl-dev \
        libxml2-dev \
        libz-dev \
        zlib1g-dev

RUN apt-get install -y --no-install-recommends \
        libfreetype6-dev \
        libjpeg-dev \
        libpng12-dev \
    && docker-php-ext-configure gd \
        --enable-gd-native-ttf \
        --with-jpeg-dir=/usr/lib \
        --with-freetype-dir=/usr/include/freetype2 \
    && docker-php-ext-install gd

RUN docker-php-ext-configure imap \
        --with-imap-ssl \
        --with-kerberos \
    && docker-php-ext-install imap

RUN docker-php-ext-install \
        bcmath \
        mbstring \
        mcrypt \
        mysqli \
        opcache \
        recode \
        readline \
        soap \
        xml

RUN pecl install redis \
    && pecl install zip \
    && pecl install memcache \
    echo "extension=memcache.so" > /usr/local/etc/php/conf.d/ext-memcache.ini

RUN yes | pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini
#
# RUN apt-get install -y --no-install-recommends libmagickwand-6.q16-dev  \
# 	&& ln -s /usr/lib/x86_64-linux-gnu/ImageMagick-6.8.9/bin-Q16/MagickWand-config /usr/bin/ \
# 	&& pecl install imagick \
# 	&& echo "extension=imagick.so" > /usr/local/etc/php/conf.d/ext-imagick.ini \
# 	&& rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y libmagickwand-6.q16-dev --no-install-recommends \
  && ln -s /usr/lib/x86_64-linux-gnu/ImageMagick-6.8.9/bin-Q16/MagickWand-config /usr/bin \
  && pecl install imagick
  # && echo "extension=imagick.so" > /usr/local/etc/php/conf.d/ext-imagick.ini

RUN docker-php-ext-enable imagick
RUN docker-php-ext-enable mcrypt
RUN docker-php-ext-enable memcache
RUN docker-php-ext-enable opcache
RUN docker-php-ext-enable redis
RUN docker-php-ext-enable xdebug

RUN usermod -u 1000 www-data

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000
