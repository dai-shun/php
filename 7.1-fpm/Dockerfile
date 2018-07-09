FROM php:7.1-fpm

RUN apt-get update \
    && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libicu-dev \
        wget \
        git \
        cron \
        dos2unix \
            --no-install-recommends

# root用户使用composer不警告
ENV COMPOSER_ALLOW_SUPERUSER 1
# 默认远程调试指向本地
ENV XDEBUG_REMOTE_HOST 127.0.0.1
# 默认调试端口为9000
ENV XDEBUG_REMOTE_PORT 9000
# 默认不启用调试
ENV XDEBUG_REMOTE_ENABLE 0

COPY ./install-composer.sh /
COPY ./php.ini /usr/local/etc/php/
COPY ./www.conf /usr/local/etc/php/
COPY ./extensions/memcached-3.0.4.tgz /memcached.tgz
COPY ./extensions/redis-4.0.2.tgz /redis.tgz
COPY ./extensions/xdebug-2.6.0.tgz /xdebug.tgz



RUN docker-php-ext-install zip intl mbstring pdo_mysql exif bcmath sysvsem shmop\
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd

RUN docker-php-ext-install sockets

# xdebug
RUN pear install /xdebug.tgz \
       && rm -rf /tmp/pear \
       && rm -rf /xdebug.tgz \
       && docker-php-ext-enable xdebug

# memcached
RUN apt-get install -y libmemcached-dev zlib1g-dev \
   && pear install /memcached.tgz \
   && rm -rf /tmp/pear \
   && rm -rf /memcached.tgz \
   && docker-php-ext-enable memcached

# redis
RUN pear install /redis.tgz \
       && rm -rf /tmp/pear \
       && rm -rf /redis.tgz \
       && docker-php-ext-enable redis


RUN apt-get purge -y g++ \
    && apt-get autoremove -y \
    && rm -r /var/lib/apt/lists/* \
    && rm -rf /tmp/* \
    && dos2unix -q /install-composer.sh \
    && sh /install-composer.sh \
    && rm /install-composer.sh

RUN apt-get --purge remove -y dos2unix

RUN usermod -u 1000 www-data && usermod -G staff www-data

VOLUME /root/.composer
WORKDIR /app

EXPOSE 9000
CMD ["php-fpm"]
