FROM debian:bullseye-slim

ENV MAILUSER=mailuser \
    MAILPASSWORD=mailpass

VOLUME /home/$MAILUSER/Maildir

EXPOSE 143

RUN apt update && \
    apt upgrade -y && \
    apt install -y dovecot-imapd

RUN useradd -m $MAILUSER && \
    echo $MAILUSER:$MAILPASSWORD | chpasswd && \
    sed -i.dist 's%^#disable_plaintext_auth = yes$%disable_plaintext_auth = no%' /etc/dovecot/conf.d/10-auth.conf && \
    sed -i 's%^auth_mechanisms = plain$%auth_mechanisms = plain login%' /etc/dovecot/conf.d/10-auth.conf && \
    sed -i.dist 's%^#log_path = syslog$%log_path = /proc/self/fd/1%' /etc/dovecot/conf.d/10-logging.conf && \
    sed -i.dist 's%^mail_location =.*$%mail_location = maildir:~/Maildir%' /etc/dovecot/conf.d/10-mail.conf && \
    mkdir -p /home/$MAILUSER/Maildir && \
    chown -R $MAILUSER:$MAILUSER /home/$MAILUSER/Maildir

STOPSIGNAL SIGINT

CMD dovecot -F
