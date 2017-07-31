FROM ubuntu:xenial

LABEL maintainer="Mriyam Tamuli <mbtamuli@gmail.com>"

RUN export GNUPGHOME="$(mktemp -d)" \
    && gpg --keyserver "hkp://pgp.mit.edu" --recv-keys '3050AC3CD2AE6F03' \
    && gpg -a --export --armor '3050AC3CD2AE6F03' | apt-key add - \
    && rm -r "$GNUPGHOME"

RUN echo "deb http://download.opensuse.org/repositories/home:/rtCamp:/EasyEngine/xUbuntu_16.04/ /" >> /etc/apt/sources.list \
    && apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get \
      -o Dpkg::Options::="--force-confmiss" \
      -o Dpkg::Options::="--force-confold" \
      -y install nginx-custom nginx-ee \
    && rm -rf /var/lib/apt/lists/*

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]
