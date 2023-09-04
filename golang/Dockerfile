FROM docker.io/rockylinux:9.0

LABEL maintainer=med@mehmetemredogan.com.tr

ARG SSH_PRIVATE_KEY

ENV PATH /usr/local/go/bin:/root/go/bin:$PATH
ENV GOLANG_VERSION 1.21.0
ENV GOROOT=/usr/local/go
ENV GODIR=/usr/local/go
ENV GOEXEC=/usr/local/go/bin/go
ENV SHA256=b945ae2bb5db01a0fb4786afde64e6fbab50b67f6fa0eb6cfa4924f16a7ff1eb

RUN dnf update -y --setopt=install_weak_deps=False

RUN dnf install -y --setopt=install_weak_deps=False \
	nano \
	curl \
	gcc \
	g++ \
	make \
	git \
	pkg-config \
	ca-certificates

RUN cd /tmp
RUN	curl -L -O "https://go.dev/dl/go${GOLANG_VERSION}.linux-amd64.tar.gz"

RUN echo "${SHA256} go${GOLANG_VERSION}.linux-amd64.tar.gz" | sha256sum -c

RUN tar -C /usr/local -xf "go${GOLANG_VERSION}.linux-amd64.tar.gz"
RUN rm -rf go${GOLANG_VERSION}.linux-amd64.tar.gz

# SSH Key
RUN echo ${SSH_PRIVATE_KEY} > ~/.ssh/id_ed25519
RUN chmod 600 ~/.ssh/id_ed25519
RUN sed -i 's/ /\n/g' ~/.ssh/id_ed25519
RUN perl -i -pe 'BEGIN{undef $/;} s/BEGIN*[\r\n]*OPENSSH*[\r\n]*PRIVATE*[\r\n]*KEY/BEGIN OPENSSH PRIVATE KEY/smg' ~/.ssh/id_ed25519
RUN perl -i -pe 'BEGIN{undef $/;} s/END*[\r\n]*OPENSSH*[\r\n]*PRIVATE*[\r\n]*KEY/END OPENSSH PRIVATE KEY/smg' ~/.ssh/id_ed25519

# git clone non-interactive
RUN touch ~/.ssh/known_hosts
RUN ssh-keyscan github.com >> ~/.ssh/known_hosts

# Build => docker build --build-arg SSH_PRIVATE_KEY="$(cat ~/.ssh/id_ed25519)" -t go:1.21.0 .