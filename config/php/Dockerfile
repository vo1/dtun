FROM webdevops/php-apache-dev:${PHP_VERSION}
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
    sudo

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ && docker-php-ext-install gd
RUN composer global require hirak/prestissimo
RUN sudo -u application composer -- global require hirak/prestissimo
