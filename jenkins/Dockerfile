FROM jenkins/jenkins:lts-jdk17

USER root
# Docker CLI so pipeline "agent { docker { ... } }" can start sibling containers via the host socket
RUN apt-get update && apt-get install -y --no-install-recommends docker.io git \
    && rm -rf /var/lib/apt/lists/* \
    && usermod -aG docker jenkins
USER jenkins

COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN jenkins-plugin-cli --plugin-file /usr/share/jenkins/ref/plugins.txt
