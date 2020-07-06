FROM php:7.2-fpm

COPY composer.json index.php phpunit.xml .htaccess /var/www/html/
RUN mkdir /var/www/html/config
RUN mkdir /var/www/html/data
COPY config/* /var/www/html/config/
COPY data/* /var/www/html/data/

RUN usermod -u 1000 www-data
RUN usermod -G staff www-data

RUN apt-get update && apt-get install -y libpng-dev libjpeg-dev libpq-dev libzstd-dev zip unzip sudo wget sqlite3 libsqlite3-dev && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr
RUN docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd
RUN docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql
RUN docker-php-ext-configure mysqli --with-mysqli=mysqlnd

RUN yes | pecl install igbinary redis

RUN docker-php-ext-install pdo && \
    docker-php-ext-install pdo_mysql && \
    docker-php-ext-install mysqli && \
    docker-php-ext-install pgsql && \
    docker-php-ext-install pdo_pgsql && \
    docker-php-ext-install pdo_sqlite && \
    docker-php-ext-install gd && \
    docker-php-ext-install mbstring && \
    docker-php-ext-install opcache && \
    docker-php-ext-install zip && \
    docker-php-ext-install calendar && \
    docker-php-ext-install sockets && \
    docker-php-ext-install pcntl && \
    docker-php-ext-enable igbinary && \
    docker-php-ext-enable redis

RUN { \
    echo 'opcache.memory_consumption=128'; \
    echo 'opcache.interned_strings_buffer=8'; \
    echo 'opcache.max_accelerated_files=4000'; \
    echo 'opcache.revalidate_freq=2'; \
    echo 'opcache.fast_shutdown=1'; \
    echo 'opcache.enable_cli=1'; \
    echo 'opcache.load_comments=1'; \
} >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini
   
RUN curl -sS https://getcomposer.org/installer | php && \
    php composer.phar install && \
    find / | grep composer\.phar | xargs -n 1 rm && \
    rm -rf /root/.composer && \
    chown -R www-data:www-data /var/www/html && \
    chown -R www-data:www-data /var/www/html/config

RUN wget https://hub.taotesting.com/resources/taohub-articles/articles/third-party/MathJax_Install_TAO_3x.sh
RUN chmod u+x MathJax_Install_TAO_3x.sh
RUN ./MathJax_Install_TAO_3x.sh

#RUN rm -rf /var/www/html \
#    && chmod 0777 /tmp/
RUN chmod 0777 /tmp/

COPY phpinfo.php /var/www/html/

# RUN echo >&2 "Installing TAO..."
# RUN sudo -u www-data php tao/scripts/taoInstall.php \
#     --db_driver pdo_mysql \
#     --db_host tao_db:3306 \
#     --db_name "tao" \
#     --db_user "tao" \
#     --db_pass "tao" \
#     --module_url "http://localhost:8088" \
#     --user_login "admin" \
#     --user_pass "P@ssw0rd" \
#     -e taoCe \
#     --verbose