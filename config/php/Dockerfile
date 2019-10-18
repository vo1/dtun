ARG IMAGE_PHP
FROM php:${IMAGE_PHP}-apache

ARG IMAGE_PHP
ARG USER_UID
ARG USER_GID
ARG APACHE_DOCROOT

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
    libmagickwand-dev

RUN docker-php-ext-configure gd --with-gd --with-jpeg-dir --with-png-dir --with-zlib-dir --with-freetype-dir && docker-php-ext-install gd
RUN pecl install imagick
RUN docker-php-ext-enable imagick

RUN if [ "${IMAGE_PHP}" != "7.2" ]; then \
    apt-get install -y ssmtp; \
    docker-php-ext-configure mcrypt; \
    docker-php-ext-install mcrypt; \
    echo "FromLineOverride=YES" >> /etc/ssmtp/ssmtp.conf; \
    echo 'sendmail_path = "/usr/sbin/ssmtp -t"' > /usr/local/etc/php/conf.d/mail.ini; \
fi;

RUN docker-php-ext-install \ 
    intl \
    bcmath \
    soap \
    zip \
    pdo_mysql \
    xsl

RUN curl --silent --show-error https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN if [ "${IMAGE_PHP}" != "5.6" ]; then \
    yes | pecl install xdebug; \
    echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini; \
    echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini; \
    echo "xdebug.remote_connect_back=on" >> /usr/local/etc/php/conf.d/xdebug.ini; \
    echo "xdebug.remote_port=9000" >> /usr/local/etc/php/conf.d/xdebug.ini; \
    echo "xdebug.remote_log=/tmp/xdebug.log" >> /usr/local/etc/php/conf.d/xdebug.ini; \
    echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini; \
    echo "memory_limit=-1" >> /usr/local/etc/php/conf.d/xdebug.ini; \
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

RUN ln -s /var/www/html /app && \
    groupadd -g ${USER_GID} app && \
    useradd -r -u ${USER_UID} -g app -d /var/www app && \
    chown app:app /var/www

USER app

RUN composer global require hirak/prestissimo

FROM php:7.1-apache

RUN sed -ri -e 's!/var/www/html!${APACHE_DOCROOT}!g' /etc/apache2/sites-available/*.conf
