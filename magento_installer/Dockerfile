FROM php:7.4-apache as builder

##
## Install composer
WORKDIR /tmp

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php -r "if (hash_file('sha384', 'composer-setup.php') === '55ce33d7678c5a611085589f1f3ddf8b3c52d662cd01d4ba75c0ee0459970c2200a51f492d557530c71c15d8dba01eae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv ./composer.phar /usr/local/bin/composer
RUN composer config --list --global

##
## Install dependencies
RUN apt update
RUN apt install -y libzip-dev zip libgd-dev libicu-dev libsodium-dev libpng-dev libxml2-dev libxslt1-dev 

RUN touch /usr/local/etc/php/php.ini

RUN docker-php-ext-install sodium
RUN docker-php-ext-configure gd --with-jpeg
RUN docker-php-ext-install gd
RUN docker-php-ext-install intl
RUN docker-php-ext-install pdo_mysql
RUN docker-php-ext-install zip
RUN docker-php-ext-install soap
RUN docker-php-ext-install xsl
RUN docker-php-ext-install sockets
RUN docker-php-ext-install bcmath

##
## Install Magento2
ARG MAGENTO_PUBLIC_KEY
ARG MAGENTO_PRIVATE_KEY

# Auth keys
WORKDIR /root/.composer
RUN rm -f auth.json
RUN touch ./auth.json
RUN echo "{ \"http-basic\": { \"repo.magento.com\": { \"username\": \"$MAGENTO_PUBLIC_KEY\", \"password\": \"$MAGENTO_PRIVATE_KEY\" } } }" > auth.json

# This is the install dir
RUN php --ini
RUN mkdir -p /magento
RUN composer create-project --no-install --repository-url=https://repo.magento.com/ magento/project-community-edition=2.4.5 /magento

WORKDIR /magento

RUN composer config --no-plugins allow-plugins.laminas/laminas-dependency-plugin true
RUN composer config --no-plugins allow-plugins.dealerdirect/phpcodesniffer-composer-installer true
RUN composer config --no-plugins allow-plugins.magento/composer-dependency-version-audit-plugin true
RUN composer config --no-plugins allow-plugins.magento/composer-root-update-plugin true
RUN composer config --no-plugins allow-plugins.magento/inventory-composer-installer true
RUN composer config --no-plugins allow-plugins.magento/magento-composer-installer true

RUN composer install

RUN echo "memory_limit = 2048M\n" > /usr/local/etc/php/php.ini
