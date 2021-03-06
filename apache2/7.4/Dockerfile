FROM benlumia007/ubuntu:20.04

## Here, we are going to set the frontend to no interactive.
ENV DEBIAN_FRONTEND=noninteractive

# Add a ppa repository  for git so that we can the latest version
RUN add-apt-repository ppa:git-core/ppa -y

## Here we are going to run and update and upgrade packages.
RUN apt-get update -y

# Here, we are going to add some basic requirements
RUN apt-get install --no-install-recommends \
    git \
    gnupg \
    python3-pip \
    python-setuptools \
    python3-wheel \
    -y

# Here, we are going to install Web Server ( NGINX )
RUN apt-get install --no-install-recommends \
    apache2 \
    libapache2-mod-php7.4 \
    mysql-client \
    php-imagick \
    php-memcache \
    php-memcached \
    php-pear \
    php-ssh2 \
    php-xdebug \
    php7.4-bcmath \
    php7.4-cli \
    php7.4-common \
    php7.4-curl \
    php7.4-dev \
    php7.4-gd \
    php7.4-imap \
    php7.4-imagick \
    php7.4-json \
    php7.4-mbstring \
    php7.4-mysql \
    php7.4-soap \
    php7.4-sqlite3 \
    php7.4-xml \
    php7.4-zip \
    -y

RUN a2enmod headers ssl rewrite

# Install composer
RUN wget https://getcomposer.org/download/2.0.4/composer.phar
RUN chmod +x composer.phar
RUN mv composer.phar /usr/local/bin/composer

# Install NPM/Node
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash
RUN apt-get install --no-install-recommends \
    nodejs \
    -y
    
# Here, we are going to install shyaml
RUN pip3 install shyaml

# Here, we will be adding a custom file to enable features for php-custom.ini
RUN { \
    echo 'error_reporting=E_ALL'; \
    echo 'display_errors=On'; \
    echo 'display_startup_errors=On'; \
    echo 'error_log=/var/log/php/php_errors.log'; \
    echo 'log_errors=On'; \
    echo 'log_errors_max_len=0'; \
    echo 'max_execution_time=120'; \
    echo 'max_input_vars=3000'; \
    echo 'memory_limit=512M'; \
    echo 'post_max_size=100M'; \
    echo 'upload_max_filesize=100M'; \
} > /etc/php/7.4/mods-available/php-custom.ini


# Here, we are going to enable opcache
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=2'; \
		echo 'opcache.fast_shutdown=1'; \
} > /etc/php/7.4/mods-available/php-opcache.ini

COPY apache2/7.4/config/mailhog/mhsendmail /usr/local/bin/mhsendmail
RUN chmod +x /usr/local/bin/mhsendmail
RUN echo 'sendmail_path = /usr/local/bin/mhsendmail --smtp-addr mailhog:1025' > /etc/php/7.4/mods-available/mailhog.ini

# Here, we will be enable php-custom.ini using phpenmod
RUN phpenmod php-custom && \
    phpenmod php-opcache && \
    phpenmod mailhog && \
    phpenmod mysqli && \
    phpenmod mysqlnd && \
    phpdismod xdebug

# Here, we are going to install wp cli as docker user
RUN curl --silent -L -o /usr/local/bin/wp https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar && \
    chmod +x /usr/local/bin/wp

WORKDIR /app

RUN usermod -u 1000 www-data
RUN chown -R www-data:www-data /var/www

# Create an alias for wp
RUN echo "alias wp='sudo -EH -u "www-data" "$@" wp'" >> /root/.bashrc
RUN /bin/bash -c "source /root/.bashrc"

CMD apachectl -D FOREGROUND