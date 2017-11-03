FROM php:7-fpm
MAINTAINER satoshi.fujiu

RUN apt-get update && apt-get install -y libmcrypt-dev mysql-client \
    && docker-php-ext-install mcrypt pdo_mysql && \
    #zip,unzip
    apt-get install -y zip unzip && \
    #vim
    apt-get install -y vim curl

    #composer
RUN curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    chmod 777 /usr/local/bin/composer

RUN cd /var/www && \
    curl https://get.fuelphp.com/oil | sh

WORKDIR /var/www