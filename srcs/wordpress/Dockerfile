FROM debian:buster

RUN apt-get update && apt-get -y install wget php7.3 php-mysqli php-fpm mariadb-client

COPY ./www.conf /etc/php/7.3/fpm/pool.d

RUN wget -P /usr/local/bin https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar && \
    chmod -R 777 /usr/local/bin/wp-cli.phar && \
    mv /usr/local/bin/wp-cli.phar /usr/local/bin/wp && \
    mkdir -p /var/www/wordpress && \
    mkdir ./run/php/

COPY ./wp-config.php ./var/www/wordpress/

ARG DB_NAME
ARG DB_USER
ARG DB_PASS
ARG DB_HOST

# Fix some of the variables
RUN sed -i "s/DB_NAME/$DB_NAME/g" ./var/www/wordpress/wp-config.php && \
    sed -i "s/DB_USER/$DB_USER/g" ./var/www/wordpress/wp-config.php && \
    sed -i "s/DB_PASS/$DB_PASS/g" ./var/www/wordpress/wp-config.php && \ 
    sed -i "s/DB_HOST/$DB_HOST/g" ./var/www/wordpress/wp-config.php

RUN cd /var/www/wordpress && wp core download --allow-root

#Installing Wordpress and executing process
CMD cd /var/www/wordpress && \
    wp core install --allow-root \
                    --url=$WP_URL \
                    --title=$WP_TITLE \
                    --admin_user=$WP_ADMIN_NICK \
                    --admin_password=$WP_ADMIN_PASS \
                    --admin_email=$WP_ADMIN_MAIL && \
    DOES_USER_EXIST=$(wp user --allow-root list | grep $WP_USER_NICK | wc -l) && \
    if [ $DOES_USER_EXIST -eq 0 ] ; then \
    wp user create --allow-root $WP_USER_NICK $WP_USER_MAIL --user_pass=$WP_USER_PASS ; fi && \
    /usr/sbin/php-fpm7.3 -F
