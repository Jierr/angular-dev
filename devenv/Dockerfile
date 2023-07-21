FROM ubuntu:jammy as image-angular-base
LABEL Description="Development Base Container with Ubuntu 20.04 LTS - For Angular and typescript development"

ENV USER_NAME=devel
ENV USER_ID=1000
ENV GROUP_ID=1000
ENV DBUS_SESSION_BUS_ADDRESS=unix:path=/var/run/user/$USER_NAME/terminal-dbus.socket
ENV LANGUAGE=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8
ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /work
USER root

RUN apt-get update && apt-get -y dist-upgrade && apt-get -y install \
    sudo debianutils build-essential cmake dbus iputils-ping git curl ssh locales vim 

RUN rm /bin/sh && ln -s bash /bin/sh
RUN locale-gen en_US.utf8 en_GB.utf8 de_DE.utf8 && update-locale

RUN groupadd -g $USER_ID $USER_NAME && \
  useradd -m -u $USER_ID -g $GROUP_ID -d /home/$USER_NAME -s /bin/bash $USER_NAME && \
  usermod -a -G adm,cdrom,sudo,dip,plugdev $USER_NAME && \
  echo "$USER_NAME:$USER_NAME" | chpasswd && \
  echo "$USER_NAME   ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Prepare dbus init & local signing with key.
RUN mkdir -p /var/run/user/$USER_NAME && \
  chown -R $USER_NAME:$USER_NAME /var/run/user/$USER_NAME

# Taken from - https://docs.docker.com/engine/examples/running_ssh_service/#environment-variables
# SSH login fix. Otherwise user is kicked off after login
RUN mkdir /var/run/sshd && \
  echo 'root:root' | chpasswd && \
  sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
  sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
  
# Update Node PPA
RUN curl -sL https://deb.nodesource.com/setup_18.x -o nodesource_setup.sh && chmod 755 nodesource_setup.sh && ./nodesource_setup.sh
RUN apt-get update && apt-get -y install nodejs \
    && apt-get autoremove -y && apt-get clean

RUN npm install -g typescript @angular/cli

USER $USER_NAME
WORKDIR /home/$USER_NAME
