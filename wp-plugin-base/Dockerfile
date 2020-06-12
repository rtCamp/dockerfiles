FROM php:7.4-apache

LABEL maintainer="Thrijith Thankachan <thrijith13@gmail.com>, Riddhesh Sanghvi <riddhesh.sanghvi@rtcamp.com>"

# Install basic requirements
# Install Node (with NPM), and Yarn (via package manager for Debian)
# Install Subversion
# Install gulp and grunt
RUN apt update \
 && apt install -y \
 curl \
 apt-transport-https \
 git \
 build-essential \
 libssl-dev \
 wget \
 unzip \
 bzip2 \
 libbz2-dev \
 zlib1g-dev \
 mariadb-client \
 libfontconfig \
 libfreetype6-dev \
 libjpeg62-turbo-dev \
 libpng-dev \
 libicu-dev \
 libxml2-dev \
 libldap2-dev \
 libmcrypt-dev \
 gnupg \
 subversion rsync \
 && curl -sL https://deb.nodesource.com/setup_12.x | bash - \
 && apt update \
 && apt install -y nodejs \
 && apt clean -y && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
 && npm install -g yarn grunt-cli gulp-cli

#
# Install Composer
#
ENV PATH "/composer/vendor/bin:$PATH"
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /composer
ENV COMPOSER_VERSION 1.8.4

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
&& php -r "if (hash_file('sha384', 'composer-setup.php') === 'e0012edf3e80b6978849f5eff0d4b4e4c79ff1609dd1e613307e16318854d24ae64f26d17af3ef0bf7cfb710ca74755a') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
&& php composer-setup.php --no-ansi --install-dir=/usr/bin --filename=composer --version=${COMPOSER_VERSION} \
&& php -r "unlink('composer-setup.php');" \
&& composer --ansi --version --no-interaction

#
# PHP configuration
#
# Increase PHP memory limit
RUN echo "memory_limit=-1" > $PHP_INI_DIR/conf.d/memory-limit.ini
