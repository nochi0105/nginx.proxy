FROM php:fpm

RUN apt-get update && apt-get install -y --no-install-recommends \
        libfreetype6-dev \
        vim \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
		zlib1g-dev libicu-dev g++ \
        fontconfig-config fonts-dejavu-core wget locales \
    && docker-php-ext-install -j$(nproc) iconv exif \
    && docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd

RUN docker-php-ext-install -j$(nproc) mysqli pdo_mysql
RUN docker-php-ext-configure intl
RUN docker-php-ext-install intl
# php-pear #

RUN mkdir /php && chown www-data:www-data /php && chmod 770 /php

# echo listen = [::]:9000 \
RUN { \
      echo [global] ;\
      echo daemonize = no ;\
      echo ;\
      echo [www] ;\
      echo listen = /php/php.sock ;\
      echo listen.owner = www-data ;\
      echo listen.group = www-data ;\
      echo listen.mode = 0660 ;\
      echo slowlog = /proc/self/fd/2 ;\
      echo request_slowlog_timeout = 10s ;\
      echo security.limit_extensions = .php ;\
      echo rlimit_files = 10240 ;\
      echo php_value[output_buffering] = Off ;\
      echo php_admin_value[cgi.fix_pathinfo] = 0 ;\
      echo php_admin_value[upload_max_filesize] = 2G ;\
      echo php_admin_value[post_max_size] = 2G ;\
      echo php_admin_value[session.save_path] = /tmp ;\
      echo php_admin_value[pdo_mysql.default_socket] = /run/mysqld/mysqld.sock ;\
      echo php_admin_value[mysql.default_socket] = /run/mysqld/mysqld.sock ;\
      echo php_admin_value[mysqli.default_socket] = /run/mysqld/mysqld.sock ;\
    } | tee /usr/local/etc/php-fpm.d/zz-docker.conf

RUN sed -i -e 's/# ja_JP.UTF-8 UTF-8/ja_JP.UTF-8 UTF-8/' /etc/locale.gen && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=ja_JP.UTF-8


# RUN locale-gen en_US.UTF-8 && DEBIAN_FRONTEND=noninteractive dpkg-reconfigure locales

# NOTE: make sure to chown your data folder to 33:33

# RUN chmod -R 660 /srv/www
# RUN chmod 777 /srv/www/pydio-core/data/files/
# RUN chmod 777 /srv/www/pydio-core/data/personal/

CMD php-fpm
