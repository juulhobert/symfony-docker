FROM php:7-apache
ADD apache2.conf /etc/apache2/apache2.conf
ADD 000-default.conf /etc/apache2/sites-available/000-default.conf
ADD symfony.ini /usr/local/etc/php/conf.d/symfony.ini
ADD mpm_prefork.conf /etc/apache2/mods-available/mpm_prefork.conf
RUN rm /etc/apache2/conf-available/security.conf && rm /etc/apache2/conf-enabled/security.conf

RUN apt-get update \
    && apt-get install -y libicu-dev git wget libpng-dev libjpeg62-turbo-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-configure gd --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install pdo pdo_mysql opcache intl zip gd \
    && a2enmod rewrite headers \
    && mkdir /var/www/html/web

RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer

# Install Node
ENV NPM_CONFIG_LOGLEVEL notice
ENV NODE_VERSION 8.9.1

RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \
    && tar -xJf "node-v$NODE_VERSION-linux-x64.tar.xz" -C /usr/local --strip-components=1 \
    && rm "node-v$NODE_VERSION-linux-x64.tar.xz" \
    && ln -s /usr/local/bin/node /usr/local/bin/nodejs

# Upload settings
RUN echo "file_uploads = On\n" \
         "memory_limit = 256M\n" \
         "upload_max_filesize = 256M\n" \
         "post_max_size = 256M\n" \
         "max_execution_time = 30\n" \
         > /usr/local/etc/php/conf.d/uploads.ini

# https://getcomposer.org/doc/03-cli.md#composer-allow-superuser
ENV COMPOSER_ALLOW_SUPERUSER 1

RUN composer global require "hirak/prestissimo:^0.3" --prefer-dist --no-progress --no-suggest --optimize-autoloader --classmap-authoritative \
    && composer clear-cache
