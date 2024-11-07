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

COPY --chown=root:root rsyslog.conf /etc/rsyslog.conf

RUN curl -fsSL https://deb.nodesource.com/setup_20.x -o /tmp/nodesource_setup.sh && \
    chmod u+x /tmp/nodesource_setup.sh && bash /tmp/nodesource_setup.sh

RUN apt-get update && apt install -y nodejs

RUN echo 'while [ 1 ]; do sleep 1 ;done;' > /loop_inf.sh && chmod +x /loop_inf.sh

FROM debian-nodejs

RUN git clone -b v3 https://github.com/Mailtrain-org/mailtrain.git /opt/mailtrain-v3

CMD systemctl restart rsyslog.service && \
    /loop_inf.sh