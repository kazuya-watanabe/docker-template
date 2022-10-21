FROM debian:bullseye-slim

ENV MYSQL_ROOT_PASSWORD=password

VOLUME /var/lib/mysql

EXPOSE 3306

RUN apt update && \
    apt upgrade -y && \
    apt install -y mariadb-server

RUN mkdir -p /run/mysqld && \
    chown mysql:mysql /run/mysqld && \
    sed -i.dist 's%^bind-address\s*= 127.0.0.1$%bind-address = 0.0.0.0%' /etc/mysql/mariadb.conf.d/50-server.cnf && \
    mysqld & \
    sleep 5 && \
    echo "GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '$MYSQL_ROOT_PASSWORD' WITH GRANT OPTION" | mysql -uroot && \
    echo "GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' IDENTIFIED BY '$MYSQL_ROOT_PASSWORD' WITH GRANT OPTION" | mysql -uroot && \
    mysqladmin -uroot -p$MYSQL_ROOT_PASSWORD shutdown

STOPSIGNAL SIGTERM

CMD mysqld
