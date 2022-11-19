FROM docker.io/rockylinux:9.0

LABEL maintainer=med@mehmetemredogan.com.tr

ARG SSH_PRIVATE_KEY

ENV PATH /usr/local/go/bin:/root/go/bin:$PATH
ENV GOLANG_VERSION 1.19.3
ENV GOROOT=/usr/local/go
ENV GODIR=/usr/local/go
ENV GOEXEC=/usr/local/go/bin/go
ENV SHA256=74b9640724fd4e6bb0ed2a1bc44ae813a03f1e72a4c76253e2d5c015494430ba

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
RUN mkdir ~/.ssh
RUN echo ${SSH_PRIVATE_KEY} > ~/.ssh/id_ed25519
RUN chmod 600 ~/.ssh/id_ed25519
RUN sed -i 's/ /\n/g' ~/.ssh/id_ed25519
RUN perl -i -pe 'BEGIN{undef $/;} s/BEGIN*[\r\n]*OPENSSH*[\r\n]*PRIVATE*[\r\n]*KEY/BEGIN OPENSSH PRIVATE KEY/smg' ~/.ssh/id_ed25519
RUN perl -i -pe 'BEGIN{undef $/;} s/END*[\r\n]*OPENSSH*[\r\n]*PRIVATE*[\r\n]*KEY/END OPENSSH PRIVATE KEY/smg' ~/.ssh/id_ed25519

# git clone non-interactive
RUN touch ~/.ssh/known_hosts
RUN ssh-keyscan github.com >> ~/.ssh/known_hosts

# Build => docker build --build-arg SSH_PRIVATE_KEY="$(cat ~/.ssh/id_ed25519)" -t go:1.19.3 .