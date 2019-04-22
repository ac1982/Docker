FROM php:7.3-alpine

ENV MYSQL_XDEVAPI_VER 8.0.15

# Add a non-root user:
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

RUN addgroup -g ${PGID} laradock \
    && adduser -D -G laradock -u ${PUID} laradock \
    && apk add --no-cache --update build-base autoconf curl protobuf protobuf-dev boost boost-dev \
    && mkdir -p /usr/src/temp \
    && cd /usr/src/temp \
    && curl -fSL https://pecl.php.net/get/mysql_xdevapi-$MYSQL_XDEVAPI_VER.tgz -o mysql_xdevapi-$MYSQL_XDEVAPI_VER.tgz \
    && tar xf mysql_xdevapi-$MYSQL_XDEVAPI_VER.tgz \
    && cd mysql_xdevapi-$MYSQL_XDEVAPI_VER \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && curl -fSL https://curl.haxx.se/ca/cacert.pem -o /etc/cacert.pem \
    && echo "openssl.cafile=/etc/cacert.pem" > /usr/local/etc/php/conf.d/docker-php-openssl-cafile.ini \
    && docker-php-ext-install opcache pdo_mysql pcntl \
    && pecl install redis \
    && docker-php-ext-enable opcache pdo_mysql pcntl redis mysql_xdevapi \
    && apk del tar xz-libs xz build-base autoconf protobuf-dev boost-dev \
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php \
    && mv composer.phar /usr/local/bin/composer \
    && php -r "unlink('composer-setup.php');" \
    && rm -rf /usr/src /tmp/pear ~/.pearrc /usr/src/temp \
    && echo "* * * * * laradock /usr/bin/php /var/www/artisan schedule:run >> /var/www/storage/logs/crontabs.log 2>&1" > /etc/crontabs/schedule \
    && chmod -R 644 /etc/crontabs
