FROM alpine:3.7
LABEL maintainer="Dharmin Shah <dharminshah175@gmail.com>"

RUN apk update \
    && apk --no-cache add dnsmasq
RUN mkdir -p /etc/default/
RUN echo -e "ENABLED=1\nIGNORE_RESOLVCONF=yes" > /etc/default/dnsmasq
RUN echo "addn-hosts=/etc/ee4_hosts" > /etc/dnsmasq.d/ee.conf
ENTRYPOINT ["dnsmasq","--no-daemon"]
