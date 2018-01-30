FROM wordpress:php7.1-fpm

RUN set -ex; \
	apt-get update; \
	apt-get install -y \
	imagemagick \
	less \
	libfreetype6-dev \
	libjpeg62-turbo-dev \
	libmagickwand-dev \
	libmemcached-dev \
	libpcre3 \
	libpcre3-dev \
	libpng12-dev \
	libssl-dev \
	libz-dev \
	ssmtp \
	unzip; \
	rm -rf /var/lib/apt/lists/*;

RUN pecl install imagick
RUN docker-php-ext-enable imagick

# Install Memcache
RUN apt-get install --no-install-recommends -y  && \
	mkdir -p /usr/src/php/ext && \
	cd /usr/src/php/ext/ && \
	curl -sSL -o php7.zip https://github.com/websupport-sk/pecl-memcache/archive/NON_BLOCKING_IO_php7.zip && \
	unzip php7.zip && \
	mv pecl-memcache-NON_BLOCKING_IO_php7 memcache && \
	docker-php-ext-configure memcache --with-php-config=/usr/local/bin/php-config && \
	docker-php-ext-install memcache && \
	echo "extension=memcache.so" > /usr/local/etc/php/conf.d/ext-memcache.ini && \
	rm -rf /tmp/pecl-memcache-php7 php7.zip
RUN docker-php-ext-install memcache

# Donwload and install composer
RUN curl -sSL "https://getcomposer.org/installer" | php \
	&& mv composer.phar /usr/local/bin/composer

# Install wp-cli
RUN curl -O "https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar" \
	&& chmod +x wp-cli.phar \
	&& mv wp-cli.phar /usr/local/bin/wp

# Setup a config file
RUN mkdir -p /etc/wp-cli
RUN { \
	echo 'path: /var/www/html'; \
	} > /etc/wp-cli/config.yml

RUN echo "mailhub=mail:1025\nUseTLS=NO\nFromLineOverride=YES" > /etc/ssmtp/ssmtp.conf
