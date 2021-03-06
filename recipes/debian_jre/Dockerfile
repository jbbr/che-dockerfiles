# Copyright (c) 2012-2016 Codenvy, S.A.
# All rights reserved. This program and the accompanying materials
# are made available under the terms of the Eclipse Public License v1.0
# which accompanies this distribution, and is available at
# http://www.eclipse.org/legal/epl-v10.html
# Contributors:
# Codenvy, S.A. - initial API and implementation

FROM debian:jessie
ENV JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64 \
ENV PATH $JAVA_HOME/bin:$PATH
RUN echo "deb http://ftp.debian.org/debian testing main" >> /etc/apt/sources.list && \
    apt-get update && \
    apt-get -y install \
    openssh-server \
    sudo \
    procps \
    openjdk-8-jre-headless \
    wget \
    unzip \
    mc \
    locales \
    ca-certificates \
    curl && \
    mkdir /var/run/sshd && \
    sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd && \
    echo "%sudo ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers && \
    useradd -u 1000 -G users,sudo -d /home/user --shell /bin/bash -m user
RUN PASS=$(openssl rand -base64 32) && \
    echo "$PASS\n$PASS" | passwd user && \
    sudo echo -e "deb http://ppa.launchpad.net/git-core/ppa/ubuntu precise main\ndeb-src http://ppa.launchpad.net/git-core/ppa/ubuntu precise main" >> /etc/apt/sources.list.d/sources.list && \
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys A1715D88E1DF1F24 && \
    sudo apt-get install git subversion -y && \
    apt-get clean && \
    apt-get -y autoremove \
    && apt-get -y clean \
    && rm -rf /var/lib/apt/lists/* && \
    echo "#! /bin/bash\n set -e\n sudo /usr/sbin/sshd -D &\n exec \"\$@\"" > /home/user/entrypoint.sh && chmod a+x /home/user/entrypoint.sh
ENV LANG C.UTF-8
USER user
RUN sudo localedef -i en_US -f UTF-8 en_US.UTF-8 && \
    svn --version && \
    sed -i 's/# store-passwords = no/store-passwords = yes/g' /home/user/.subversion/servers && \
    sed -i 's/# store-plaintext-passwords = no/store-plaintext-passwords = yes/g' /home/user/.subversion/servers
EXPOSE 22 4403
WORKDIR /projects
ENTRYPOINT ["/home/user/entrypoint.sh"]
CMD tail -f /dev/null

