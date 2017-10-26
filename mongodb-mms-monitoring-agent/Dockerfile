FROM    alpine as builder

ARG     VERSION=6.0.0.381-1

RUN     apk add --no-cache --virtual .build-deps \
            curl \
            tar
RUN     curl -sL -o - https://s3.amazonaws.com/mongodb-mms-build-agent/releases/prod/mongodb-mms-monitoring-agent-$VERSION.linux_x86_64.tar.gz | tar xz -C /usr/local/bin --strip-components 1 && \
        chmod +x /usr/local/bin/mongodb-mms-monitoring-agent

###

FROM        debian

RUN         apt-get update && \
            apt-get install -y libsasl2-2 && \
            rm -rf /var/lib/apt/lists/* && \
            mkdir -p /var/lib/mongodb-mms-automation /var/log/mongodb-mms-automation /data && \
            ln -sf /dev/stdout /var/log/mongodb-mms-automation/monitoring-agent.log
VOLUME      /data
ENTRYPOINT  ["mongodb-mms-monitoring-agent"]
COPY        --from=builder /usr/local/bin/mongodb-mms-monitoring-agent /usr/local/bin/
