ARG DEPOT
ARG TAG="bookworm-slim"
ARG SOURCELIST="sourcelist/bookworm.list"

FROM ${DEPOT}debian:${TAG} AS debian-nodejs

LABEL autor="matyas-cyril"

COPY --chown=root:root --chmod=0644 ${SOURCELIST} /etc/apt/sources.list
COPY --chown=root:root --chmod=0644 bashrc /root/.bashrc

RUN apt-get update && apt-get upgrade -y && \
    apt-get install --no-install-recommends -y \
    lsof vim vim-common rsyslog rsyslog-doc htop btop \
    ca-certificates openssl curl git \
    man-db jq htop db-util locate gettext-base systemctl

COPY --chown=root:root --chmod=744 docker-entrypoint.sh /app/docker-entrypoint.sh

# Installer Node.js v20
RUN curl -fsSL https://deb.nodesource.com/setup_20.x -o /tmp/nodesource_setup.sh && \
    chmod u+x /tmp/nodesource_setup.sh && bash /tmp/nodesource_setup.sh

RUN apt-get update && apt install -y nodejs netcat-openbsd pwgen imagemagick

COPY --chown=root:root rsyslog.conf /etc/rsyslog.conf
#RUN echo 'while [ 1 ]; do sleep 1 ;done;' > /loop_inf.sh && chmod +x /loop_inf.sh

FROM debian-nodejs

RUN git clone -b v3 https://github.com/Mailtrain-org/mailtrain.git /opt/mailtrain-v3

RUN mkdir -p /app/server /app/client /app/shared /app/zone-mta && \
    cp -f /opt/mailtrain-v3/server/package.json /app/server/package.json && \
    cp -f /opt/mailtrain-v3/server/package-lock.json /app/server/package-lock.json && \
    cp -f /opt/mailtrain-v3/client/package.json /app/client/package.json && \
    cp -f /opt/mailtrain-v3/client/package-lock.json /app/client/package-lock.json && \
    cp -f /opt/mailtrain-v3/shared/package.json /app/shared/package.json && \
    cp -f /opt/mailtrain-v3/shared/package-lock.json /app/shared/package-lock.json && \
    cp -f /opt/mailtrain-v3/zone-mta/package.json /app/zone-mta/package.json && \
    cp -f /opt/mailtrain-v3/zone-mta/package-lock.json /app/zone-mta/package-lock.json

# Installer les dépendances pour chaque rep
RUN for dir in /app/client /app/shared /app/server /app/zone-mta; do (cd $dir && npm install); done

WORKDIR /app/

EXPOSE 3000 3003 3004

CMD systemctl restart rsyslog.service && \
    /app/docker-entrypoint.sh    
    #/loop_inf.sh