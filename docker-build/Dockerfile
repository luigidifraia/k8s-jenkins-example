FROM jenkins/jenkins:lts

LABEL maintainer="Luigi Di Fraia"

USER root

# From https://docs.docker.com/engine/install/debian/

# Docker CE installation
RUN apt-get update && apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -

RUN add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
    $(lsb_release -cs) \
    stable"

RUN apt-get update && apt-get install -y \
    docker-ce \
    docker-ce-cli \
    containerd.io

RUN usermod -a -G docker jenkins

# From https://kubernetes.io/docs/tasks/tools/install-kubectl/

# Kubectl installation
RUN apt-get update && apt-get install -y \
    apt-transport-https \
    gnupg2

RUN curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

RUN add-apt-repository \
    "deb [arch=amd64] https://apt.kubernetes.io \
    kubernetes-xenial \
    main"

RUN apt-get update && apt-get install -y kubectl=1.18.8-00

# Install default plugins
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt

RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt

USER jenkins
