ARG IMAGE_PHP
FROM php:${IMAGE_PHP}-apache

ARG IMAGE_PHP
ARG USER_UID
ARG USER_GID
ARG XDEBUG

RUN ln -s /var/www/html /app && \
    groupadd -g ${USER_GID} app && \
    useradd -r -u ${USER_UID} -g app -d /var/www app && \
    chown app:app /var/www

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
    libxslt1-dev \
    libmagickwand-dev \
    libzip-dev \
    libgmp-dev \
    libbz2-dev \
    git \
    zip \
    curl \
    dirmngr \
    apt-transport-https \
    lsb-release \
    ca-certificates \
    dos2unix

RUN echo "sendmail_path = \"/usr/bin/msmtp -C /etc/msmtp.conf -t\"" > /usr/local/etc/php/conf.d/mail.ini

RUN pecl install imagick
RUN docker-php-ext-enable imagick

RUN docker-php-ext-install \ 
    intl \
    bcmath \
    soap \
    zip \
    pdo_mysql \
    xsl \
    sockets \
    pcntl \
    gmp \
    bz2

RUN pecl install -f redis
RUN docker-php-ext-enable redis

RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -
RUN curl --silent --show-error https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version=1.10.20
RUN curl -sS -O https://files.magerun.net/n98-magerun2-latest.phar && curl -sS -o n98-magerun2-latest.phar.sha256 https://files.magerun.net/sha256.php?file=n98-magerun2-latest.phar && shasum -a 256 -c n98-magerun2-latest.phar.sha256 && chmod +x ./n98-magerun2-latest.phar && cp ./n98-magerun2-latest.phar /usr/local/bin/magerun2
RUN apt -y install nodejs
RUN npm install -g yarn

RUN echo "memory_limit=-1" >> /usr/local/etc/php/conf.d/memory.ini

RUN if [ "${IMAGE_PHP}" != "5.6" ]; then \
    yes | pecl install xdebug-2.9.2; \
fi;

RUN if [ "${IMAGE_PHP}" = "5.6" ]; then \
    docker-php-ext-install xml; \ 
    cd /tmp; \
    curl -o ioncube.tar.gz http://downloads3.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz; \
    tar -xvvzf ioncube.tar.gz; \
    mv ioncube/ioncube_loader_lin_5.6.so /usr/local/lib/php/extensions/*; \
    rm -Rf ioncube.tar.gz ioncube; \
    echo "zend_extension=ioncube_loader_lin_5.6.so" > /usr/local/etc/php/conf.d/00_docker-php-ext-ioncube_loader_lin_5.6.ini; \
fi;


RUN if [ "${IMAGE_PHP}" != "7.4" ]; then \
    docker-php-ext-configure gd --with-gd --with-jpeg-dir --with-png-dir --with-zlib-dir --with-freetype-dir && docker-php-ext-install gd; \
fi;

RUN if [ "${IMAGE_PHP}" = "7.4" ]; then \
    docker-php-ext-configure gd --with-freetype --with-jpeg; \
    docker-php-ext-install gd; \
fi;


#RUN sed -ri -e 's!/var/www/html!/var/www/html/pub!g' /etc/apache2/sites-available/*.conf
#RUN apt install -y mariadb-client-core

#RUN echo "zend_extension=xdebug.so" > /usr/local/etc/php/conf.d/xdebug.ini; \
#    echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini; \
#    echo "xdebug.remote_connect_back=on" >> /usr/local/etc/php/conf.d/xdebug.ini; \
#    echo "xdebug.remote_port=9000" >> /usr/local/etc/php/conf.d/xdebug.ini; \
#    echo "xdebug.remote_log=/tmp/xdebug.log" >> /usr/local/etc/php/conf.d/xdebug.ini; \
#    echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini;

RUN a2enmod proxy_http

USER app
RUN composer global require hirak/prestissimo
