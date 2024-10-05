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

RUN a2enmod rewrite ssl

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

RUN curl -sS -O https://files.magerun.net/n98-magerun2-latest.phar && curl -sS -o n98-magerun2-latest.phar.sha256 https://files.magerun.net/sha256.php?file=n98-magerun2-latest.phar && shasum -a 256 -c n98-magerun2-latest.phar.sha256 && chmod +x ./n98-magerun2-latest.phar && cp ./n98-magerun2-latest.phar /usr/local/bin/magerun2

RUN echo "memory_limit=-1" >> /usr/local/etc/php/conf.d/memory.ini


RUN if [ "${IMAGE_PHP}" = "5.6" ]; then \
    docker-php-ext-install xml; \ 
    cd /tmp; \
    curl -o ioncube.tar.gz http://downloads3.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz; \
    tar -xvvzf ioncube.tar.gz; \
    mv ioncube/ioncube_loader_lin_5.6.so /usr/local/lib/php/extensions/*; \
    rm -Rf ioncube.tar.gz ioncube; \
    echo "zend_extension=ioncube_loader_lin_5.6.so" > /usr/local/etc/php/conf.d/00_docker-php-ext-ioncube_loader_lin_5.6.ini; \
fi;


RUN if [ "${IMAGE_PHP}" != "7.4" ] && [ "${IMAGE_PHP}" != "8.1" ]; then \
    docker-php-ext-configure gd --with-gd --with-jpeg-dir --with-png-dir --with-zlib-dir --with-freetype-dir && docker-php-ext-install gd; \
fi;

RUN if [ "${IMAGE_PHP}" = "7.4" ] || [ "${IMAGE_PHP}" = "8.1" ]; then \
    docker-php-ext-configure gd --with-freetype --with-jpeg; \
    docker-php-ext-install gd; \
fi;

RUN if [ "${IMAGE_PHP}" = "7.3" ]; then \
    pecl install pcov; \
    docker-php-ext-enable pcov; \
fi;

RUN a2enmod proxy_http
RUN echo "    ForwardAgent yes" >> /etc/ssh/ssh_config

#RUN sed -ri -e 's!/var/www/html!/var/www/html/pub!g' /etc/apache2/sites-available/*.conf
#RUN apt install -y mariadb-client-core
#RUN if [ "${IMAGE_PHP}" = "8.1" ] || [ "${IMAGE_PHP}" = "7.4" ]; then \
#    yes | pecl install xdebug-3.1.5; \
#    echo "zend_extension=xdebug.so" > /usr/local/etc/php/conf.d/xdebug.ini; \
#    echo "xdebug.mode=debug" >> /usr/local/etc/php/conf.d/xdebug.ini; \
#    echo "xdebug.start_with_request=debug" >> /usr/local/etc/php/conf.d/xdebug.ini; \
#    echo "xdebug.client_host=10.10.10.10" >> /usr/local/etc/php/conf.d/xdebug.ini; \
#    echo "xdebug.client_port=9000" >> /usr/local/etc/php/conf.d/xdebug.ini; \
#fi;

#RUN ssh-add -K

ARG NODE_VERSION
RUN curl --silent --show-error https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version=2.2.10;
RUN echo "Setting node version to ${NODE_VERSION}"
ENV NODE_VERSION=${NODE_VERSION}
ENV NVM_DIR=/usr/local/nvm
RUN mkdir ${NVM_DIR}

RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
#RUN mv /root/.nvm ${NVM_DIR} && chown -r ${USER_UID}:${USER:GID} ${NVM_DIR}

# RUN . "$NVM_DIR/nvm.sh" && nvm ls-remote
RUN . "$NVM_DIR/nvm.sh" && nvm install ${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm use v${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm alias default v${NODE_VERSION}
ENV PATH="${NVM_DIR}/versions/node/v${NODE_VERSION}/bin/:${PATH}"

RUN npm install -g yarn
RUN node --version
RUN npm --version

RUN openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout /srv.key -out /srv.csr -subj="/C=NL/CN=dev/O=dev/OU=dev/"
RUN sed -i -e "s/\w*SSLCertificateFile.*/SSLCertificateFile \/srv.csr/g" -e "s/\w*SSLCertificateKeyFile.*/SSLCertificateKeyFile \/srv.key/g" /etc/apache2/sites-available/default-ssl.conf
RUN chown ${USER_UID}:${USER_GID} /srv.key
RUN a2ensite default-ssl

USER app

# RUN eval `ssh-agent -s` && ssh-add

#RUN if [ "${IMAGE_PHP}" != "8.1" ]; then \
#    composer global require hirak/prestissimo; \
#fi;

#RUN composer global require squizlabs/php_codesniffer
#RUN composer global require phpmd/phpmd
#RUN composer global require phpstan/phpstan
ENV PATH="${PATH}:/var/www/html/vendor/bin"
