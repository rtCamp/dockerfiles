FROM ubuntu:xenial

LABEL maintainer="Mriyam Tamuli <mbtamuli@gmail.com>"

RUN apt update && apt install -qqy wget

RUN wget http://download.opensuse.org/repositories/openSUSE:Tools/xUbuntu_16.04/Release.key \
    && apt-key add - < Release.key

RUN sh -c "echo 'deb http://download.opensuse.org/repositories/openSUSE:/Tools/xUbuntu_16.04/ /' > /etc/apt/sources.list.d/osc.list"

RUN apt update && apt install -qqy \
                      debhelper \
                      devscripts \
                      dh-make \
                      dh-systemd \
                      dput \
                      git \
                      gnupg-agent \
                      osc \
                      sudo \
                      vim

COPY oscrc /root/.oscrc
