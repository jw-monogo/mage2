FROM alpine:3.7

RUN apk add --update \
    php7-fpm \
    php7-apcu \
    php7-ctype \
    php7-curl \
    php7-dom \
    php7-gd \
    php7-iconv \
    php7-imagick \
    php7-json \
    php7-intl \
    php7-ldap \
    php7-mcrypt \
    php7-fileinfo\
    php7-mbstring \
    php7-opcache \
    php7-openssl \
    php7-pdo \
    php7-pdo_mysql \
    php7-mysqli \
    php7-xml \
    php7-zlib \
    php7-phar \
    php7-tokenizer \
    php7-session \
    php7-simplexml \
    php7-xdebug \
    php7-zip \
    php7-xmlreader \
    php7-xmlwriter \
    php7-soap \
    php7-calendar \
    php7-xdebug \
    make \
    curl \
    tzdata \
    supervisor

RUN rm -rf /var/cache/apk/* && rm -rf /tmp/* && \
    curl --insecure https://getcomposer.org/composer.phar -o /usr/bin/composer && \
    chmod +x /usr/bin/composer && \
    cp /usr/share/zoneinfo/Europe/Warsaw /etc/localtime && \
    echo "Europe/Warsaw" > /etc/timezone && \
    apk del tzdata && \
    date

COPY docker/nginx/www.pool.conf /etc/php7/php-fpm.d/
COPY docker/nginx/memory.ini /etc/php7/conf.d/
CMD ["php-fpm7", "-F"]

WORKDIR /code
EXPOSE 9001
