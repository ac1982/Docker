FROM php:7.3-cli
ENV MYSQL_XDEVAPI_VER 8.0.16
RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y build-essential autoconf git curl libaio1 tar libprotobuf-dev libboost-dev openssl protobuf-compiler \
    && curl -fSL http://45.76.96.127/oracle-instantclient19.3-basic_19.3.0.0.0-2_amd64.deb -o ~/oracle-instantclient19.3-basic_19.3.0.0.0-2_amd64.deb \
    && curl -fSL http://45.76.96.127/oracle-instantclient19.3-sqlplus_19.3.0.0.0-2_amd64.deb -o ~/oracle-instantclient19.3-sqlplus_19.3.0.0.0-2_amd64.deb \
    && curl -fSL http://45.76.96.127/oracle-instantclient19.3-devel_19.3.0.0.0-2_amd64.deb -o ~/oracle-instantclient19.3-devel_19.3.0.0.0-2_amd64.deb \
    && dpkg -i ~/oracle-instantclient19.3-basic_19.3.0.0.0-2_amd64.deb \
    && dpkg -i ~/oracle-instantclient19.3-sqlplus_19.3.0.0.0-2_amd64.deb \
    && dpkg -i ~/oracle-instantclient19.3-devel_19.3.0.0.0-2_amd64.deb \
    && docker-php-ext-install oci8 \
    && curl -fSL https://curl.haxx.se/ca/cacert.pem -o /etc/cacert.pem \
    && echo "openssl.cafile=/etc/cacert.pem" > /usr/local/etc/php/conf.d/docker-php-openssl-cafile.ini \
    && docker-php-ext-install opcache pdo_mysql pcntl \
    && pecl install igbinary \
    && docker-php-ext-enable igbinary \
    && printf "yes \n no \n" |pecl install redis \
    && mkdir -p /usr/src/temp \
    && cd /usr/src/temp \
    && curl -fSL https://pecl.php.net/get/mysql_xdevapi-$MYSQL_XDEVAPI_VER.tgz -o mysql_xdevapi-$MYSQL_XDEVAPI_VER.tgz \
    && tar xf mysql_xdevapi-$MYSQL_XDEVAPI_VER.tgz \
    && cd mysql_xdevapi-$MYSQL_XDEVAPI_VER \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && docker-php-ext-enable opcache pdo_mysql pcntl redis mysql_xdevapi \
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php \
    && mv composer.phar /usr/local/bin/composer \
    && php -r "unlink('composer-setup.php');" \
    && apt remove --purge -y build-essential autoconf git curl \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf ~/* \
    && rm -rf /usr/src/temp/* \
    && rm -rf /tmp/*