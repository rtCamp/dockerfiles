FROM php:7-cli

Maintainer Mriyam Tamuli <mbtamuli@gmail.com>

RUN echo "memory_limit=-1" > "$PHP_INI_DIR/conf.d/memory-limit.ini" \
&& echo "date.timezone=Asia/Kolkata" > "$PHP_INI_DIR/conf.d/date_timezone.ini"

VOLUME /root/composer
ENV PATH "/composer/vendor/bin:$PATH"
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /composer
ENV COMPOSER_VERSION 1.3.1

# Packages
RUN apt-get update && \
  DEBIAN_FRONTEND=noninteractive apt-get install -y \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libpng12-dev \
    libbz2-dev \
    libxslt-dev \
    php-pear \
    curl \
    git \
    subversion \
    zip unzip \
    wget \
    mariadb-server \
    libmemcached-dev libmemcached11 \
  && rm -r /var/lib/apt/lists/*

# PHP Extensions
RUN docker-php-ext-install bcmath mcrypt zip bz2 mbstring pcntl xsl \
  && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
  && docker-php-ext-install gd \
  && docker-php-ext-install mysqli

RUN cd /tmp \
  && git clone https://github.com/bnap00/pecl-memcache.git memcache \
  && cd memcache \ 
  && /usr/local/bin/phpize \
  && ./configure --with-php-config=/usr/local/bin/php-config \
  && make \
  && make install \
  && echo "extension=/usr/local/lib/php/extensions/no-debug-non-zts-20160303/memcache.so" > /usr/local/etc/php/conf.d/ext-memcache.ini  \
  && rm -rf /tmp/memcache

# Start MYSQL Service and set root password as root
RUN service mysql start && mysql -e "create database wp" && mysqladmin -u root password root

# Setup the Composer installer
RUN curl -o /tmp/composer-setup.php https://getcomposer.org/installer \
  && curl -o /tmp/composer-setup.sig https://composer.github.io/installer.sig \
  && php -r "if (hash('SHA384', file_get_contents('/tmp/composer-setup.php')) !== trim(file_get_contents('/tmp/composer-setup.sig'))) { unlink('/tmp/composer-setup.php'); echo 'Invalid installer' . PHP_EOL; exit(1); }"

# Install Composer
RUN mkdir -p /composer
COPY composer.lock /composer
RUN php /tmp/composer-setup.php --no-ansi --install-dir=/usr/local/bin --filename=composer --version=${COMPOSER_VERSION} && rm -rf /tmp/composer-setup.php

RUN composer global require "deployer/deployer <5" deployer/recipes wp-cli/wp-cli

#Install phpcs
RUN composer global require squizlabs/php_codesniffer ~2.9.0 
