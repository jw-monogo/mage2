FROM alpine:3.7
LABEL maintainer="Jakub Wójtowicz<jakub.wojtowicz@monogo.pl>"
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
    php7-bcmath \
    php7-xsl \
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
    nginx \
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

RUN adduser -D -g '' -G www-data www-data
RUN mkdir /code && mkdir /root/.ssh
COPY docker/external/authorized_keys /root/.ssh/

COPY --chown=www-data:www-data . /code

RUN rm -rf /code/docker
COPY docker/external/www.pool.conf /etc/php7/php-fpm.d/
COPY docker/external/memory.ini /etc/php7/conf.d/
RUN rm /etc/nginx/conf.d/default.conf

ADD docker/external/nginx.conf /etc/nginx/
ADD docker/external/magento.conf /etc/nginx/conf.d/

COPY /docker/external/supervisord.conf /etc/supervisor/supervisord.conf

WORKDIR /code
EXPOSE 80

CMD /usr/bin/supervisord -n -c /etc/supervisor/supervisord.conf
