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
        libpq-dev \
        libssl-dev \
        libxml2-dev \
        libz-dev \
        zlib1g-dev \ 
        git

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
        xml \
        zip 

RUN pecl install redis 

RUN cd /tmp \
    && git clone https://github.com/bnap00/pecl-memcache.git memcache \
    && cd memcache \
    && /usr/local/bin/phpize \
    && ./configure --with-php-config=/usr/local/bin/php-config \
    && make \
    && make install \
    && echo "extension=/usr/local/lib/php/extensions/no-debug-non-zts-20160303/memcache.so" > /usr/local/etc/php/conf.d/ext-memcache.ini  \
    && rm -rf /tmp/memcache

RUN yes | pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini

RUN apt-get update && apt-get install -y libmagickwand-6.q16-dev --no-install-recommends \
  && ln -s /usr/lib/x86_64-linux-gnu/ImageMagick-6.8.9/bin-Q16/MagickWand-config /usr/bin \
  && pecl install imagick

RUN docker-php-ext-enable \
                    imagick \
                    mcrypt \
                    opcache \
                    redis \
                    xdebug

RUN usermod -u 1000 www-data

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000
