# Copyright (c) 2012-2016 Codenvy, S.A. and LamdaFu, LLC
# All rights reserved. This program and the accompanying materials
# are made available under the terms of the Eclipse Public License v1.0
# which accompanies this distribution, and is available at
# http://www.eclipse.org/legal/epl-v10.html
# Contributors:
# Codenvy, S.A.	- initial API and implementation
# LamdaFu, LLC 	- added BigTop 1.1.0 fork of ubuntu_jdk8 Dockerfile

FROM ubuntu:trusty
MAINTAINER https://github.com/LamdaFu/dockerfiles/issues

EXPOSE 4403 8000 8080 9876 22
RUN apt-get update && \
    apt-get -y install sudo openssh-server procps wget unzip mc curl subversion nmap software-properties-common python-software-properties vim && \
    mkdir /var/run/sshd && \
    sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd && \
    echo "%sudo ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers && \
    useradd -u 1000 -G users,sudo -d /home/user --shell /bin/bash -m user && \
    echo "secret\nsecret" | passwd user && \
    add-apt-repository ppa:git-core/ppa && \
    add-apt-repository ppa:openjdk-r/ppa && \
    apt-get update && \
    sudo apt-get install git -y && \
    sudo apt-get install openjdk-8-jdk-headless -y && \
    sudo update-ca-certificates -f && \
    sudo sudo /var/lib/dpkg/info/ca-certificates-java.postinst configure && \
    apt-get clean && \
    apt-get -y autoremove && \
    rm -rf /var/lib/apt/lists/*

USER user

LABEL che:server:8080:ref=tomcat8 che:server:8080:protocol=http che:server:8000:ref=tomcat8-debug che:server:8000:protocol=http che:server:9876:ref=codeserver che:server:9876:protocol=http

ENV MAVEN_VERSION=3.3.9 \
    JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64 \
    TOMCAT_HOME=/home/user/tomcat8

ENV M2_HOME=/home/user/apache-maven-$MAVEN_VERSION

ENV PATH=$JAVA_HOME/bin:$M2_HOME/bin:$PATH

RUN mkdir /home/user/cbuild /home/user/tomcat8 /home/user/apache-maven-$MAVEN_VERSION && \
    wget -qO- "http://apache.ip-connect.vn.ua/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz" | tar -zx --strip-components=1 -C /home/user/apache-maven-$MAVEN_VERSION/
ENV TERM xterm

RUN wget -qO- "http://archive.apache.org/dist/tomcat/tomcat-8/v8.0.24/bin/apache-tomcat-8.0.24.tar.gz" | tar -zx --strip-components=1 -C /home/user/tomcat8 && \
    rm -rf /home/user/tomcat8/webapps/*

ENV LANG en_GB.UTF-8
ENV LANG en_US.UTF-8
RUN sudo locale-gen en_US.UTF-8 && \
    svn --version && \
    sed -i 's/# store-passwords = no/store-passwords = yes/g' /home/user/.subversion/servers && \
    sed -i 's/# store-plaintext-passwords = no/store-plaintext-passwords = yes/g' /home/user/.subversion/servers

RUN echo "Setting up Bigtop 1.1.0"
RUN wget -O- http://archive.apache.org/dist/bigtop/bigtop-1.1.0/repos/GPG-KEY-bigtop | sudo apt-key add -
RUN sudo wget -O /etc/apt/sources.list.d/bigtop-1.1.0.list \
		http://archive.apache.org/dist/bigtop/bigtop-1.1.0/repos/`lsb_release --codename --short`/bigtop.list
RUN sudo apt-get update
RUN sudo apt-get -y install hadoop-client hive pig sqoop flume 

WORKDIR /projects

CMD sudo /usr/sbin/sshd -D && \
    tail -f /dev/null
