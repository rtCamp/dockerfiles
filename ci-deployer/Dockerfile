FROM composer:1.6.2 AS composer

FROM php:7.1-cli

LABEL maintainer="Mriyam Tamuli <mbtamuli@gmail.com>"

RUN echo "memory_limit=-1" > "$PHP_INI_DIR/conf.d/memory-limit.ini" \
&& echo "date.timezone=Asia/Kolkata" > "$PHP_INI_DIR/conf.d/date_timezone.ini"

VOLUME /root/composer
ENV HOME /root
ENV PATH "/composer/vendor/bin:~/.local/bin:/usr/local/rvm/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:$PATH"
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /composer
ENV COMPOSER_VERSION 1.6.2
ENV NVM_DIR "$HOME/.nvm"
ENV NODE_VERSION lts
ENV RVM_DIR "/etc/profile.d/rvm.sh"

# Packages
RUN apt-get update && \
  DEBIAN_FRONTEND=noninteractive apt-get install -y \
	  apt-transport-https \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libpng12-dev \
    libbz2-dev \
    libxslt-dev \
    curl \
    git \
    subversion \
    zip unzip \
    wget \
    mariadb-server \
    libmemcached-dev libmemcached11 \
    libssl-dev \
    libreadline-dev \
    python-pip \
    python-dev \
    libyaml-dev \
  && rm -r /var/lib/apt/lists/*

RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# create user 'ruby' and install all rvm-stuff as that user
RUN command curl -sSL https://rvm.io/mpapis.asc | gpg --homedir /root/.gnupg --import -
## Ruby
RUN curl -L https://get.rvm.io | bash -s stable
RUN /bin/bash -l -c "rvm requirements"
RUN /bin/bash -l -c "rvm install 2.4.1"
RUN /bin/bash -l -c "gem install bundler --no-ri --no-rdoc"

# Installing awscli
RUN pip install --quiet awscli --upgrade --user

# PHP Extensions
RUN docker-php-ext-install bcmath mcrypt zip bz2 mbstring pcntl xsl \
  && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir \
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
# Multi stage
COPY --from=composer /usr/bin/composer /usr/bin/composer
RUN mkdir -p /composer
COPY composer.* /composer/
RUN cd /composer && composer install

# Download and load nvm in environment
RUN wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.6/install.sh | bash
# install node and npm
RUN bash -c "[ -s $NVM_DIR/nvm.sh ] && \. $NVM_DIR/nvm.sh \
    && nvm install --lts \
    && nvm use default"

# install yarn
RUN bash -c "[ -s $NVM_DIR/nvm.sh ] && \. $NVM_DIR/nvm.sh \
    && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
    && echo \"deb https://dl.yarnpkg.com/debian/ stable main\" | tee /etc/apt/sources.list.d/yarn.list \
    && apt-get update > /dev/null && apt-get install yarn -qqy \
    && yarn config set cache-folder ~/.yarn_cache"
