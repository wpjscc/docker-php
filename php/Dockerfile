ARG PHP_VERSION="php:7.4.7-fpm-alpine"
FROM ${PHP_VERSION}

ARG TZ="Asia/Shanghai"
ARG PHP_EXTENSIONS="pdo_mysql,mysqli,mbstring,gd,curl,opcache,bcmath,zip,pcntl,redis,ssh2"
ARG CONTAINER_PACKAGE_URL="mirrors.ustc.edu.cn"

RUN if [ $CONTAINER_PACKAGE_URL ] ; then sed -i "s/dl-cdn.alpinelinux.org/${CONTAINER_PACKAGE_URL}/g" /etc/apk/repositories ; fi


COPY ./extensions /tmp/extensions
WORKDIR /tmp/extensions
RUN chmod +x install.sh \
    && sh install.sh \
    && rm -rf /tmp/extensions

ADD ./extensions/install-php-extensions  /usr/local/bin/
ADD ./php.ini  /usr/local/etc/php/php.ini
ADD ./php-fpm.conf  /usr/local/etc/php-fpm.d/www.conf

RUN chmod uga+x /usr/local/bin/install-php-extensions

RUN apk --no-cache add tzdata \
    supervisor \
    && cp "/usr/share/zoneinfo/$TZ" /etc/localtime \
    && echo "$TZ" > /etc/timezone \
    && mkdir -p /var/log/php \
    && mkdir -p /var/log/supervisor \
    && mkdir -p /etc/supervisord.d

COPY supervisord.conf /etc/supervisord.conf
 
# Fix: https://github.com/docker-library/php/issues/240
RUN apk add gnu-libiconv libstdc++ --no-cache --repository http://${CONTAINER_PACKAGE_URL}/alpine/edge/community/ --allow-untrusted
ENV LD_PRELOAD /usr/lib/preloadable_libiconv.so php


# Install composer and change it's cache home
RUN curl -o /usr/bin/composer https://mirrors.aliyun.com/composer/composer.phar \
    && chmod +x /usr/bin/composer
ENV COMPOSER_HOME=/tmp/composer

# php image's www-data user uid & gid are 82, change them to 1000 (primary user)
RUN apk --no-cache add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data


WORKDIR /www
