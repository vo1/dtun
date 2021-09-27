ARG IMAGE_PHP
FROM php:${IMAGE_PHP}-apache

ARG IMAGE_PHP
ARG USER_UID
ARG USER_GID
ARG XDEBUG

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
    libmagickwand-dev \
    libzip-dev \
    msmtp

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
    sockets

RUN curl --silent --show-error https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version=1.10.20

RUN curl -sS -O https://files.magerun.net/n98-magerun2-latest.phar && curl -sS -o n98-magerun2-latest.phar.sha256 https://files.magerun.net/sha256.php?file=n98-magerun2-latest.phar && shasum -a 256 -c n98-magerun2-latest.phar.sha256 && chmod +x ./n98-magerun2-latest.phar && cp ./n98-magerun2-latest.phar /usr/local/bin/magerun2

RUN echo "memory_limit=-1" >> /usr/local/etc/php/conf.d/memory.ini

RUN if [ "${IMAGE_PHP}" != "5.6" ]; then \
    yes | pecl install xdebug-2.7.2; \
    echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini; \
    echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini; \
    echo "xdebug.remote_connect_back=on" >> /usr/local/etc/php/conf.d/xdebug.ini; \
    echo "xdebug.remote_port=9000" >> /usr/local/etc/php/conf.d/xdebug.ini; \
    echo "xdebug.remote_log=/tmp/xdebug.log" >> /usr/local/etc/php/conf.d/xdebug.ini; \
    echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini; \
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

#RUN if [ "${IMAGE_PHP}" != "7.2" ]; then \
#    docker-php-ext-configure mcrypt; \
#    docker-php-ext-install mcrypt; \
#fi;

RUN ln -s /var/www/html /app && \
    groupadd -g ${USER_GID} app && \
    useradd -r -u ${USER_UID} -g app -d /var/www app && \
    chown app:app /var/www


RUN apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates && curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt -y install nodejs
RUN npm install -g yarn
RUN apt -y install dos2unix

RUN if [ "${IMAGE_PHP}" != "7.4" ]; then \
    docker-php-ext-configure gd --with-gd --with-jpeg-dir --with-png-dir --with-zlib-dir --with-freetype-dir && docker-php-ext-install gd; \
fi;

RUN if [ "${IMAGE_PHP}" = "7.4" ]; then \
    docker-php-ext-configure gd --with-freetype --with-jpeg; \
    docker-php-ext-install gd; \
#    docker-php-ext-configure gd --with-jpeg=/usr/include/ --with-freetype=/usr/include/; \
fi;

RUN if [ "${XDEBUG}" != "1" ]; then \
    rm -rf /usr/local/etc/php/conf.d/xdebug.ini; \
fi;

RUN pecl install -f redis
RUN docker-php-ext-enable redis
RUN docker-php-ext-install pcntl

RUN apt install -y libgmp-dev libbz2-dev && docker-php-ext-install gmp bz2

RUN sed -ri -e 's!/var/www/html!/var/www/html/pub!g' /etc/apache2/sites-available/*.conf
#RUN apt install -y mariadb-client-core

#RUN echo "sendmail_path = /usr/sbin/sendmail -S mail:1025" > /usr/local/etc/php/conf.d/mail.ini

USER app

RUN composer global require hirak/prestissimo
