FROM php:7.3.7-cli-alpine3.9

ENV PHPREDIS_VERSION=4.3.0 \
    SWOOLE_VERSION=4.3.5 \
    TIMEZONE="Asia/Shanghai"

RUN cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini \
&& sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
&& apk update \
&& apk add --no-cache --virtual .build-deps \
    curl \
    wget \
    less \
    tzdata \
    autoconf \
    gcc \
    g++ \
    make \
&& apk add zip \
    unzip \
    openssl \
    libjpeg \
    libpng-dev \
    libzip-dev \
    nghttp2-dev \
    pcre-dev  \
    freetype-dev \
    openssl-dev \
    openssl \
&& cp /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
&& php -r "readfile('https://getcomposer.org/installer');" | php \
&& mv composer.phar /usr/local/bin/composer \
# 安装php拓展
&& docker-php-ext-install bcmath gd pdo_mysql mbstring sockets zip sysvmsg sysvsem sysvshm \
# 安装redis拓展
&& wget http://pecl.php.net/get/redis-${PHPREDIS_VERSION}.tgz -O /tmp/redis.tar.tgz \
#&& pecl install igbinary \
&& pecl install /tmp/redis.tar.tgz \
&& rm -rf /tmp/redis.tar.tgz \
&& docker-php-ext-enable redis \
# 安装swoole拓展
&& wget https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz -O swoole.tar.gz \
&& mkdir -p swoole \
&& tar -xf swoole.tar.gz -C swoole --strip-components=1 \
&& rm swoole.tar.gz \
&& ( \
    cd swoole \
    && phpize \
    && ./configure --enable-mysqlnd --enable-sockets --enable-openssl --enable-http2 \
    && make -j$(nproc) \
    && make install \
) \
&& rm -r swoole \
&& docker-php-ext-enable swoole \
&& apk del --no-network .build-deps \
&& echo "[Date]\ndate.timezone=${TIMEZONE}" > /usr/local/etc/php/conf.d/timezone.ini
