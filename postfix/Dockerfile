FROM debian:bullseye-slim

ENV MAILUSER=mailuser \
    MAILPASSWORD=mailpass

VOLUME /home/$MAILUSER/Maildir

EXPOSE 25

RUN apt update && \
    apt upgrade -y && \
    DEBIAN_FRONTEND=noninteractive apt install -y postfix

COPY ./transport /etc/postfix/transport

RUN useradd -d /home/$MAILUSER -m -p $MAILPASSWORD $MAILUSER && \
    sed -i.dist 's%^myhostname =.*$%myhostname = postfix%' /etc/postfix/main.cf && \
    sed -i.dist 's%^mydestination =.*$%mydestination = $myhostname, localhost.localdomain, localhost%' /etc/postfix/main.cf && \
    { \
      echo; \
      echo 'home_mailbox = Maildir/'; \
      echo 'local_recipient_maps ='; \
      echo "luser_relay = $MAILUSER"; \
      echo 'transport_maps = hash:/etc/postfix/transport'; \
      echo 'maillog_file_prefixes = /proc, /var, /dev/stdout'; \
      echo 'maillog_file = /proc/self/fd/1'; \
    } >> /etc/postfix/main.cf && \
    postmap /etc/postfix/transport && \
    mkdir -p /home/$MAILUSER/Maildir && \
    chown -R $MAILUSER:$MAILUSER /home/$MAILUSER/Maildir

STOPSIGNAL SIGTERM

CMD postfix -c /etc/postfix start-fg
