FROM debian:bullseye-slim

WORKDIR /var/www/html

EXPOSE 80

RUN apt update && \
    apt upgrade -y && \
    DEBIAN_FRONTEND=noninteractive apt install -y apache2 php phpmyadmin roundcube roundcube-sqlite3

RUN sed -i.dist 's%^ErrorLog \${APACHE_LOG_DIR}/error.log$%ErrorLog /proc/self/fd/2%' /etc/apache2/apache2.conf && \
    sed -i.dist 's%ErrorLog \${APACHE_LOG_DIR}/error.log$%ErrorLog /proc/self/fd/2%' /etc/apache2/sites-available/000-default.conf && \
    sed -i 's%CustomLog \${APACHE_LOG_DIR}/access.log combined$%CustomLog /proc/self/fd/1 combined%' /etc/apache2/sites-available/000-default.conf && \
    sed -i.dist 's%^#AddDefaultCharset UTF-8$%AddDefaultCharset UTF-8%' /etc/apache2/conf-available/charset.conf && \
    sed -i.dist 's%^\$dbname=%//$dbname=%' /etc/phpmyadmin/config-db.php && \
    cp -a /etc/phpmyadmin/config.inc.php /etc/phpmyadmin/config.inc.php.dist && \
    { \
      echo; \
      echo "\$cfg['Servers'][\$i]['host'] = 'mariadb';"; \
    } >> /etc/phpmyadmin/config.inc.php && \
    sed -i.dist 's%^#\s*Alias /roundcube %Alias /roundcube %' /etc/roundcube/apache.conf && \
    sed -i.dist 's%^\$basepath=.*$%$basepath='\''/var/lib/roundcube/database'\'';%' /etc/roundcube/debian-db.php && \
    sed -i 's%^\$dbtype=.*$%$dbtype='\''sqlite3'\'';%' /etc/roundcube/debian-db.php && \
    sed -i.dist 's%^\$config\['\''default_host'\''\] =.*$%$config['\''default_host'\''] = '\''dovecot'\'';%' /etc/roundcube/config.inc.php && \
    sed -i 's%^\$config\['\''smtp_server'\''\] =.*$%$config['\''smtp_server'\''] = '\''postfix'\'';%' /etc/roundcube/config.inc.php && \
    sed -i 's%^\$config\['\''smtp_port'\''\] =.*$%$config['\''smtp_port'\''] = 25;%' /etc/roundcube/config.inc.php && \
    rm -f /etc/apache2/mods-enabled/autoindex.load \
          /etc/apache2/mods-enabled/autoindex.conf \
          /etc/apache2/mods-enabled/negotiation.load \
          /etc/apache2/mods-enabled/negotiation.conf \
          /etc/apache2/mods-enabled/status.load \
          /etc/apache2/mods-enabled/status.conf \
          /etc/apache2/mods-enabled/alias.conf \
          /etc/apache2/conf-enabled/localized-error-pages.conf \
          /etc/apache2/conf-enabled/other-vhosts-access-log.conf \
          /etc/apache2/conf-enabled/serve-cgi-bin.conf && \
    ln -s /etc/apache2/mods-available/expires.load /etc/apache2/mods-enabled/expires.load && \
    ln -s /etc/apache2/mods-available/headers.load /etc/apache2/mods-enabled/headers.load && \
    ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-enabled/phpmyadmin.conf && \
    mkdir -p /var/lib/roundcube/database && \
    sqlite3 /var/lib/roundcube/database/roundcube < /usr/share/roundcube/SQL/sqlite.initial.sql && \
    chown -R www-data:www-data /var/lib/roundcube/database

STOPSIGNAL SIGWINCH

CMD apachectl -DFOREGROUND
