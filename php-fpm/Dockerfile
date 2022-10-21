FROM debian:bullseye-slim

ENV PHP_MODULES="bcmath curl gd intl json mbstring mysql pgsql sqlite3 xml zip redis xdebug" \
    PHPFPM_PORT=9000 \
    XDEBUG_HOST=host.docker.internal \
    XDEBUG_PORT=9003

WORKDIR /var/www/html

EXPOSE ${PHPFPM_PORT}

RUN apt update && \
    apt upgrade -y && \
    apt install -y php-fpm composer && \
    for i in ${PHP_MODULES}; do apt install -y "php-${i}"; done

RUN cd /etc/php/7.4/fpm && \
    cp -a php.ini php.ini.dist && \
    sed -i.dist -e 's%^error_log = /var/log/php7.4-fpm.log$%error_log = /proc/self/fd/2%' php-fpm.conf && \
    sed -i -e 's%^;daemonize = yes$%daemonize = no%' php-fpm.conf && \
    sed -i -e 's%^;systemd_interval = 10$%systemd_interval = 0%' php-fpm.conf && \
    sed -i.dist -e "s%^listen = /run/php/php7.4-fpm.sock\$%listen = ${PHPFPM_PORT}%" pool.d/www.conf && \
    sed -i -e 's%^;access\.log = log/\$pool\.access.log$%access.log = /proc/self/fd/2%' pool.d/www.conf && \
    sed -i -e 's%^;access\.format =%access.format =%' pool.d/www.conf && \
    mkdir -p /run/php /var/log/xdebug && \
    chown www-data:www-data /run/php /var/log/xdebug

STOPSIGNAL SIGQUIT

CMD [ "php-fpm7.4" ]
