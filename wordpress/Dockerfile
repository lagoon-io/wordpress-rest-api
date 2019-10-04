FROM php:7.3-apache

# 変数定義
ARG APACHE_RUN_USER=daemon
ARG WP_LOCALE=ja
ARG WP_DB_NAME=wordpress
ARG WP_DB_USER=wordpress
ARG WP_DB_PASS=wordpress
ARG WP_DB_HOST=db
ARG WP_DB_PORT=3306
ARG WP_DB_PREFIX=wp_
ARG WP_DB_CHARSET=utf8

WORKDIR /var/www/html/wordpress

RUN apt-get update -y && apt-get install -y \
    libpng-dev \
    libmagickwand-dev \
    sendmail \
    mariadb-client \
    vim

# Apache 設定
RUN a2enmod rewrite

# PHP 設定
RUN docker-php-ext-install mysqli
RUN docker-php-ext-install gd bcmath exif
RUN pecl install imagick && docker-php-ext-enable imagick
RUN cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini
RUN chmod 666 /usr/local/etc/php/php.ini

# WordPress 設定
RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
RUN php wp-cli.phar --info
RUN chmod +x wp-cli.phar && mv wp-cli.phar /usr/local/bin/wp
RUN wp --info
RUN chown -R $APACHE_RUN_USER:$APACHE_RUN_USER /usr/sbin
RUN chown -R $APACHE_RUN_USER:$APACHE_RUN_USER /var/www/html/wordpress

USER $APACHE_RUN_USER
RUN wp core download --locale=$WP_LOCALE
RUN wp config create --dbname=$WP_DB_NAME --dbuser=$WP_DB_USER --dbpass=$WP_DB_PASS --dbhost=$WP_DB_HOST:$WP_DB_PORT --dbprefix=$WP_DB_PREFIX --dbcharset=$WP_DB_CHARSET --skip-check

EXPOSE 8080
