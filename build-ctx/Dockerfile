FROM scratch

ARG CF_DEBIAN_VERSION
ARG CF_CPUARCH_DEB_ROOTFS

ENV DEBIAN_FRONTEND=noninteractive

# add Debian root-fs
ADD cache/debian_stretch/rootfs-debian_stretch_${CF_DEBIAN_VERSION}-${CF_CPUARCH_DEB_ROOTFS}.tar.xz /

RUN \
	apt-get update \
	&& apt-get --assume-yes upgrade \
	&& apt-get --assume-yes dist-upgrade \
	&& apt-get -y --no-install-recommends install \
			git \
			build-essential \
			nano \
			less \
			locales \
			wget \
			p7zip \
			ca-certificates \
			apt-transport-https \
			gnupg2 \
			curl

# set locales
RUN \
	sed -i \
			-e 's/# de_DE.UTF-8 UTF-8/de_DE.UTF-8 UTF-8/' \
			-e 's/# de_DE ISO-8859-1/de_DE ISO-8859-1/' \
			-e 's/# de_DE@euro ISO-8859-15/de_DE@euro ISO-8859-15/' \
			-e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' \
			-e 's/# en_US ISO-8859-1/en_US ISO-8859-1/' \
			-e 's/# en_US@euro ISO-8859-15/en_US@euro ISO-8859-15/' \
			/etc/locale.gen \
	&& dpkg-reconfigure --frontend=noninteractive locales \
	&& update-locale LANG=de_DE.UTF-8

ENV LANG de_DE.UTF-8
ENV LANGUAGE de
ENV LC_ALL de_DE.UTF-8

# set up timezone
ENV TZ=Europe/Berlin
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#
RUN \
	chown root:root \
			/root/.bashrc \
	&& chmod 640 \
			/root/.bashrc

#
ARG CF_GOLANG_VER
ARG CF_CPUARCH_DEB_DIST
ENV CF_CPUARCH_DEB_DIST=${CF_CPUARCH_DEB_DIST}

# copy 'go' binary package into image
COPY cache/binary/go${CF_GOLANG_VER}.linux-${CF_CPUARCH_DEB_DIST}.tar.7z.* /root/

#
RUN \
	cd /root \
	&& 7zr x -so "go${CF_GOLANG_VER}.linux-${CF_CPUARCH_DEB_DIST}.tar.7z.001" | tar xf - -C /usr/local/ \
	&& ln -s /usr/local/go/bin/go /usr/local/bin/ \
	&& ln -s /usr/local/go/bin/gofmt /usr/local/bin/ \
	&& mkdir -p app/bin app/pkg app/src

ENV GOROOT=/usr/local/go
ENV GOPATH=/root/app
ENV PATH=$PATH:$GOROOT/bin:$GOPATH/bin

#
COPY files/bash/dot_bashrc /root/.bashrc

#
RUN \
	apt-get --quiet --yes autoclean \
	&& apt-get --quiet --yes autoremove \
	&& apt-get --quiet --yes clean \
	&& rm -rf \
			/usr/share/man \
			/usr/share/doc \
			/usr/share/icons \
			/usr/share/poppler \
			/usr/share/mime \
			/var/lib/apt/lists*

#
ENV DEBIAN_FRONTEND=newt

WORKDIR /root/app

VOLUME /root/app

#ENTRYPOINT ["/root/app/start.sh"]
