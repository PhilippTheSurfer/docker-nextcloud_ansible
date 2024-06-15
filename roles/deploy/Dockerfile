FROM debian:bookworm

LABEL maintainer="Michael Maffait"
LABEL org.opencontainers.image.source="https://github.com/Pandemonium1986/docker-debian12"

# Configure environment variables
ENV LC_ALL=de_DE.UTF-8
ENV LANG=de_DE.UTF-8
ENV LANGUAGE=de_DE.UTF-8
ENV PYTHONIOENCODING=utf8
ARG DEBIAN_FRONTEND=noninteractive

# Systemd configuration
STOPSIGNAL SIGRTMIN+3
ENV container=docker

# Install dependencies
RUN apt-get update && \
  apt-get install --yes --no-install-recommends \
  build-essential \
  locales \
  locales-all \
  openssh-server \
  python3-dev \
  python3-pip \
  python3-setuptools \
  python3-wheel \
  systemd && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Remove systemd target
RUN rm -rf /lib/systemd/system/multi-user.target.wants/* ; \
  rm -rf /etc/systemd/system/*.wants/* ; \
  rm -rf /lib/systemd/system/local-fs.target.wants/* ; \
  rm -rf /lib/systemd/system/sockets.target.wants/*udev* ; \
  rm -rf /lib/systemd/system/sockets.target.wants/*initctl* ; \
  rm -rf /lib/systemd/system/sysinit.target.wants/systemd-tmpfiles-setup* ; \
  rm -rf /lib/systemd/system/systemd-update-utmp*

#RUN for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; \
#  do apt-get remove $pkg; \
#  done

RUN apt-get update  && \
  apt-get install --yes ca-certificates curl  && \
  install -m 0755 -d /etc/apt/keyrings  && \
  curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc  && \
  chmod a+r /etc/apt/keyrings/docker.asc  && \
  echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
    tee /etc/apt/sources.list.d/docker.list > /dev/null  && \
  apt-get update

RUN apt-get install --yes docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

WORKDIR /

VOLUME [ "/tmp", "/run" ]

CMD ["/lib/systemd/systemd"]