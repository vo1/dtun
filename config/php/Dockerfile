ARG PHP_VERSION
FROM php:${PHP_VERSION}-apache
ARG USER_UID
ARG USER_GID

RUN a2enmod rewrite

RUN apt-get update && \
    apt-get install -y \
    zlib1g-dev \
    libicu-dev \
    g++ \
    mc \
    libmcrypt-dev \
    libxml2-dev \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    git \
    zip \
    curl \
    libxslt1-dev \
    sudo

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/
RUN docker-php-ext-configure mcrypt
RUN docker-php-ext-install \ 
    intl \
    bcmath \
    soap \
    gd \
    zip \
    pdo_mysql \
    mcrypt \
    xsl

RUN curl --silent --show-error https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN yes | pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_connect_back=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_port=9000" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_log=/tmp/xdebug.log" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini

RUN ln -s /var/www/html /app && \
    groupadd -g ${USER_GID} app && \
    useradd -r -u ${USER_UID} -g app -d /var/www app && \
    chown app:app /var/www

USER app

RUN composer global require hirak/prestissimo
