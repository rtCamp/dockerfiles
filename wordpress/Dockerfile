FROM php:7.2-fpm

RUN set -ex; \
	apt-get update; \
	apt-get install -y --no-install-recommends\
		imagemagick \
		less \
		mysql-client \
		ssmtp

# install the PHP extensions we need
RUN set -ex; \
	\
	savedAptMark="$(apt-mark showmanual)"; \
	\
	apt-get install -y --no-install-recommends \
		libfreetype6-dev \
		libjpeg-dev \
		libjpeg62-turbo-dev \
		libmagickwand-dev \
		libmcrypt-dev \
		libpng-dev \
		libzip-dev \
		zip \
		unzip \
	; \
	\
	mkdir -p /usr/src/php/ext; \
	cd /usr/src/php/ext/; \
	curl -sSL -o php7.zip https://github.com/websupport-sk/pecl-memcache/archive/NON_BLOCKING_IO_php7.zip; \
	unzip php7.zip; \
	mv pecl-memcache-NON_BLOCKING_IO_php7 memcache; \
	pecl install imagick redis; \
	printf "\n" | pecl install mcrypt-1.0.1; \
	docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr; \
	docker-php-ext-configure zip --with-libzip; \
	docker-php-ext-configure memcache --with-php-config=/usr/local/bin/php-config; \
	docker-php-ext-install gd memcache mysqli opcache zip; \
	docker-php-ext-enable imagick mcrypt redis; \
	\
# reset apt-mark's "manual" list so that "purge --auto-remove" will remove all build dependencies
	apt-mark auto '.*' > /dev/null; \
	apt-mark manual $savedAptMark; \
	ldd "$(php -r 'echo ini_get("extension_dir");')"/*.so \
		| awk '/=>/ { print $3 }' \
		| sort -u \
		| xargs -r dpkg-query -S \
		| cut -d: -f1 \
		| sort -u \
		| xargs -rt apt-mark manual; \
	\
	apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
	rm -rf /var/lib/apt/lists/* /tmp/pecl-memcache-php7 /usr/src/php/ext/php7.zip

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=2'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

VOLUME /var/www/html

ENV WORDPRESS_VERSION 4.9.4
ENV WORDPRESS_SHA1 0e630bf940fd586b10e099cd9195b3e825fb194c

RUN set -ex; \
	curl -o wordpress.tar.gz -fSL "https://wordpress.org/wordpress-${WORDPRESS_VERSION}.tar.gz"; \
	echo "$WORDPRESS_SHA1 *wordpress.tar.gz" | sha1sum -c -; \
# upstream tarballs include ./wordpress/ so this gives us /usr/src/wordpress
	tar -xzf wordpress.tar.gz -C /usr/src/; \
	rm wordpress.tar.gz; \
	chown -R www-data:www-data /usr/src/wordpress

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

COPY docker-entrypoint.sh /usr/local/bin/

RUN chown -R www-data:www-data /var/www

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["php-fpm"]