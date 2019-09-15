FROM alpine:3.9
LABEL maintainer "Fluentd developers <fluentd@googlegroups.com>"
LABEL Description="Fluentd docker image" Vendor="Fluent Organization" Version="1.7.1"

RUN apk update \
 && apk add --no-cache \
        ca-certificates \
        ruby ruby-irb ruby-etc ruby-webrick geoip \
        tini \
 && apk add --no-cache --virtual .build-deps \
        build-base \
        ruby-dev gnupg geoip-dev autoconf automake linux-headers \
        libmaxminddb libtool libc6-compat \
 && echo 'gem: --no-document' >> /etc/gemrc \
 && gem install oj -v 3.3.10 \
 && gem install json -v 2.2.0 \
 && gem install async-http -v 0.46.3 \
 && gem install fluentd -v 1.7.1 \
 && gem install bigdecimal -v 1.3.5 \
 && gem install fluent-plugin-geoip \ 
 && apk del .build-deps \
 && rm -rf /tmp/* /var/tmp/* /usr/lib/ruby/gems/*/cache/*.gem

RUN addgroup -S fluent && adduser -S -g fluent fluent \
    # for log storage (maybe shared with host)
    && mkdir -p /fluentd/log \
    # configuration/plugins path (default: copied from .)
    && mkdir -p /fluentd/etc /fluentd/plugins \
    # plugin install
    && fluent-gem install fluent-plugin-netflow \
    && fluent-gem install fluent-plugin-elasticsearch \
    # post plugin
    && chown -R fluent /fluentd && chgrp -R fluent /fluentd  


COPY fluent.conf /fluentd/etc/
COPY entrypoint.sh /bin/


ENV FLUENTD_CONF="fluent.conf"

ENV LD_PRELOAD=""
EXPOSE 24224 5140


USER fluent
ENTRYPOINT ["tini",  "--", "/bin/entrypoint.sh"]
CMD ["fluentd"]
