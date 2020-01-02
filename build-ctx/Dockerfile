ARG CF_DEBIAN_RELEASE
ARG CF_DEBIAN_VERSION
ARG CF_CPUARCH_DEB_DIST

FROM tsle/os-debian-${CF_DEBIAN_RELEASE}-${CF_CPUARCH_DEB_DIST}:${CF_DEBIAN_VERSION}

#
ENV DEBIAN_FRONTEND=noninteractive

RUN \
	apt-get update \
	&& apt-get --assume-yes upgrade \
	&& apt-get --assume-yes dist-upgrade \
	&& apt-get -y --no-install-recommends install \
			git \
			build-essential \
			p7zip \
			apt-transport-https \
			gnupg2

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
ENV DEBIAN_FRONTEND=dialog

WORKDIR /root/app

VOLUME /root/app

#ENTRYPOINT ["/root/app/start.sh"]
