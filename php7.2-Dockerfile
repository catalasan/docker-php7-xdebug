FROM php:7.2-apache-stretch

# Install PHP extensions
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash - \
    && apt-get update && apt-get install -y \
      libicu-dev \
      libpq-dev \
      libfreetype6 \
      mariadb-client \
      libmariadbclient-dev \
      ruby-full \
      nodejs \
      libzip-dev \
      zip \
      libpng-dev \
      libwebp-dev \
      libjpeg62-turbo-dev \
      libxpm-dev \
      libfreetype6-dev \
    && docker-php-ext-configure zip --with-libzip \
    && docker-php-ext-configure gd \
      --with-gd \
      --with-webp-dir \
      --with-jpeg-dir \
      --with-png-dir \
      --with-zlib-dir \
      --with-xpm-dir \
      --with-freetype-dir=/usr/lib/x86_64-linux-gnu/ \
    && rm -r /var/lib/apt/lists/* \
    && docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
    && docker-php-ext-install \
      intl \
      mbstring \
      mysqli \
      pcntl \
      pdo_mysql \
      pdo_pgsql \
      pgsql \
      zip \
      gd \
      opcache

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer

# SASS compiler
RUN su -c "gem install sass"

# Install Xdebug
RUN curl -fsSL 'https://xdebug.org/files/xdebug-2.7.2.tgz' -o xdebug.tar.gz \
    && mkdir -p xdebug \
    && tar -xf xdebug.tar.gz -C xdebug --strip-components=1 \
    && rm xdebug.tar.gz \
    && ( \
    cd xdebug \
    && phpize \
    && ./configure --enable-xdebug \
    && make -j$(nproc) \
    && make install \
    ) \
    && rm -r xdebug \
    && docker-php-ext-enable xdebug

# Put apache config
COPY build/apache-vhost.conf /etc/apache2/sites-available/custom-vhost.conf
RUN a2dissite 000-default.conf && a2ensite custom-vhost.conf && a2enmod rewrite && a2enmod ssl

# Change uid and gid of apache to docker user uid/gid
RUN usermod -u 1000 www-data && groupmod -g 1000 www-data

WORKDIR /var/www/html
