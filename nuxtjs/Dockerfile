FROM debian:bullseye-slim

ENV NUXT_HOST=0.0.0.0 \
    NUXT_PORT=3000 \
    STARTUP_COMMAND="dev"

WORKDIR /var/www/html

EXPOSE ${NUXT_PORT}

RUN apt update && \
    apt upgrade -y && \
    apt install -y curl git

RUN curl -fsSL https://deb.nodesource.com/setup_lts.x | bash - && \
    apt install -y nodejs && \
    corepack enable

STOPSIGNAL SIGTERM

CMD yarn ${STARTUP_COMMAND}
