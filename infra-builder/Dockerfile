FROM alpine:latest

LABEL maintainer="Mriyam Tamuli <mbtamuli@gmail.com>"

ENV TERRAFORM_VERSION 0.10.7
ENV ANSIBLE_VERSION 2.4.0

RUN apk add --update wget ca-certificates unzip python py-pip openssl bash && \
    apk --update add --virtual build-dependencies python-dev libffi-dev openssl-dev build-base && \
    pip install --upgrade pip cffi && \
    pip install ansible==${ANSIBLE_VERSION} && \
    wget -q -O /terraform.zip "https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip" && \
    unzip /terraform.zip -d /bin && \
    apk del build-dependencies && \
    rm -rf /var/cache/apk/* /terraform.zip

COPY setup_* /bin/
