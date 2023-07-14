FROM docker.io/rockylinux:9.0

LABEL maintainer=med@mehmetemredogan.com.tr

ENV NODEJS_VERSION 18.16.1

# .pem file
COPY service.pem /etc/pki/tls/certs/
COPY service-key.pem /etc/pki/tls/certs/

RUN dnf update -y --setopt=install_weak_deps=False

RUN dnf install -y --setopt=install_weak_deps=False \
    nano \
    curl \
    make \
	git \
	pkg-config \
	ca-certificates

# Install NVM (https://github.com/nvm-sh/nvm)
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
RUN source ~/.bashrc && nvm install v${NODEJS_VERSION}