FROM portainer/agent:2.18.4-alpine

# User and Group ID
ARG PUID=1024
ARG PGID=1024


RUN addgroup -g ${PGID} app && \
    adduser -u ${PUID} -G app -DH -g applications -s /bin/sh app

USER app
