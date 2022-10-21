FROM debian:bullseye-slim

VOLUME /var/lib/redis

EXPOSE 6379

RUN apt update && \
    apt upgrade -y && \
    apt install -y redis-server

RUN sed -i.dist 's%^bind 127\.0\.0\.1 ::1$%bind 0.0.0.0%' /etc/redis/redis.conf && \
    sed -i 's%^protected-mode yes$%protected-mode no%' /etc/redis/redis.conf && \
    sed -i 's%^daemonize yes$%daemonize no%' /etc/redis/redis.conf && \
    sed -i 's%^logfile /var/log/redis/redis-server.log$%logfile /proc/self/fd/1%' /etc/redis/redis.conf

STOPSIGNAL SIGTERM

CMD redis-server
