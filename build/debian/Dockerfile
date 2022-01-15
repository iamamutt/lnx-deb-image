# syntax=docker/dockerfile:1.3-labs

FROM debian:11-slim as lnx-deb-slim

ARG USER_NAME=joseph
ARG USER_GROUP=joseph
ARG USER_UID=1000
ARG USER_GID=1000
ARG TIMEZONE="America/Chicago"
ARG IMAGE_CREATED=2021-11-11T11:11:11Z
ARG IMAGE_VERSION=v0.0.0a

LABEL org.opencontainers.image.authors="Joseph Burling"
LABEL org.opencontainers.image.title="lnx-deb-image"
LABEL org.opencontainers.image.description="Test image for github actions."
LABEL org.opencontainers.image.version="$IMAGE_VERSION"
LABEL org.opencontainers.image.created="$IMAGE_CREATED"

# set default shell to exit on first error
SHELL [ "/bin/sh", "-ec" ]

# Install linux dependencies -----------------------------------------------------------
ENV TZ=${TIMEZONE}
RUN <<-EOF
	mkdir -p /usr/share/man/man1
	echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections
	apt-get update
	apt-get -qq install -y apt-utils
	apt-get -qq install -y locales wget sudo
	localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
	ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime
	echo ${TZ} > /etc/timezone
	apt-get autoremove -y
	apt-get clean -y
	rm -rf /var/lib/apt/lists/*
EOF
ENV LANG=en_US.utf8
ENV LC_ALL=en_US.utf8

# Add directories, user/group ----------------------------------------------------------
RUN	<<-EOF
	groupadd --gid ${USER_GID} --force ${USER_GROUP}
	umask a+rwx,o-wx
	chgrp --no-dereference ${USER_GID} /etc/profile.d /var/log
	chmod -R 774 /etc/profile.d /var/log
	useradd --uid ${USER_UID} --gid ${USER_GID} \
		--create-home --groups users,sudo --shell /bin/bash ${USER_NAME}
	passwd -qd ${USER_NAME}
	echo '${USER_NAME} ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers
EOF

# finalize -----------------------------------------------------------------------------
USER ${USER_UID}:${USER_GID}
WORKDIR /home/${USER_NAME}
RUN mkdir -p ./lnx-deb-image
ENTRYPOINT [ "/bin/bash", "-lc" ]
CMD [ "tail", "-f", "/dev/null" ]
