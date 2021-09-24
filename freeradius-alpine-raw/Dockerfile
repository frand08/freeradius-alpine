FROM alpine:edge

RUN apk add --no-cache freeradius freeradius-eap openssl

ENV PRIVATE_CERT=wpa2_server.crt PRIVATE_KEY=wpa2_server.key \
    CA_CERT=wpa2_ca.pem DH_FILE=wpa2_dh.pem

COPY radiusd.conf clients.conf /etc/raddb/
COPY eap /etc/raddb/mods-available
COPY site /etc/raddb/sites-available

RUN rm /etc/raddb/sites-enabled/* && \
    rm -rf /etc/raddb/certs && \
    ln -s /etc/raddb/sites-available/site /etc/raddb/sites-enabled/site && \
    mkdir /tmp/radiusd && \
    chown radius:radius /tmp/radiusd

COPY certs /etc/raddb/certs/

RUN chgrp -R radius /etc/raddb/

EXPOSE 1812/udp
USER radius

ENTRYPOINT ["/usr/sbin/radiusd"]
CMD ["-X", "-f"]
