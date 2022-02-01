# syntax=docker/dockerfile:1.3-labs

ARG IMAGE_CREATED=2021-11-11T11:11:11Z
ARG IMAGE_VERSION=v0.0.0a
ARG USER_NAME=joseph
ARG USER_GROUP=joseph
ARG USER_UID=1000
ARG USER_GID=1000
ARG TIMEZONE="America/Chicago"

FROM debian:11-slim as lnx-deb-slim-pre

ARG USER_NAME
ARG USER_GROUP
ARG USER_UID
ARG USER_GID
ARG TIMEZONE

# set default shell to exit on first error
SHELL [ "/bin/sh", "-ec" ]

# Install linux dependencies -----------------------------------------------------------
ENV TZ=${TIMEZONE}
RUN <<-EOF
	mkdir -p /usr/share/man/man1
	echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections
	apt-get update
	apt-get -qq install -y --no-install-recommends \
		apt-utils locales wget sudo ca-certificates curl gnupg lsb-release
	localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
	ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime
	echo ${TZ} > /etc/timezone
EOF
ENV LANG=en_US.utf8
ENV LC_ALL=en_US.utf8

# Add directories, user/group ----------------------------------------------------------
RUN	<<-EOF
	groupadd --gid ${USER_GID} --force ${USER_GROUP}
	umask a+rwx,o-wx
	chmod -R 774 /etc/profile.d /var/log
	useradd --uid ${USER_UID} --gid ${USER_GID} \
		--create-home --groups users,sudo,adm --shell /bin/bash ${USER_NAME}
	passwd -qd ${USER_NAME}
	echo '${USER_NAME} ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers
EOF

# setup docker in docker
RUN <<-EOF
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
	"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
	$(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
apt-get update
apt-get -qq install -y --no-install-recommends docker-ce docker-ce-cli containerd.io
apt-get autoremove -y
apt-get clean -y
rm -rf /var/lib/apt/lists/*
groupadd --gid 999 --force docker
usermod -a -G docker ${USER_NAME}
EOF

RUN mkdir -p -m 6755 /home/${USER_NAME}/lnx-deb-image && \
	chown -R ${USER_UID}:${USER_GID} /home/${USER_NAME}/lnx-deb-image

# finalize -----------------------------------------------------------------------------
FROM scratch as lnx-deb-slim
COPY --from=lnx-deb-slim-pre / /

ARG IMAGE_CREATED
ARG IMAGE_VERSION
ARG USER_NAME
ARG USER_GROUP
ARG USER_UID
ARG USER_GID
ARG TIMEZONE

LABEL org.opencontainers.image.authors="Joseph Burling"
LABEL org.opencontainers.image.title="lnx-deb-image"
LABEL org.opencontainers.image.description="Test image for github actions."
LABEL org.opencontainers.image.version="$IMAGE_VERSION"
LABEL org.opencontainers.image.created="$IMAGE_CREATED"

ENV TZ=${TIMEZONE}
ENV LANG=en_US.utf8
ENV LC_ALL=en_US.utf8

USER ${USER_UID}:${USER_GID}
WORKDIR /home/${USER_NAME}
ENTRYPOINT [ "/bin/bash", "-lc" ]
CMD [ "tail", "-f", "/dev/null" ]
