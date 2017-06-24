FROM python:3-slim

RUN { set -ex; \
	echo "deb http://ftp.debian.org/debian jessie-backports main" > /etc/apt/sources.list.d/bp.list; \
	apt-get update; \
	apt-get install --no-install-recommends -y ca-certificates libleveldb1; \
	apt-get install -t jessie-backports --no-install-recommends -y gosu; \
	rm -rf /etc/apt/sources.list.d/bp.list /var/lib/apt/lists/*; \
}

COPY requirements.txt /tmp/
RUN { set -ex; \
	# add user & group
	groupadd --system --gid 1000 electrumx-server; \
	useradd --system --uid 1000 --gid 1000 electrumx-server; \

	BUILD_DEPS="git build-essential libleveldb-dev"; \
	apt-get update; \
	apt-get install --no-install-recommends -y $BUILD_DEPS; \

	# install deps
	pip install --no-cache-dir -r /tmp/requirements.txt; \

	# download
	git clone https://github.com/kyuupichan/electrumx.git /tmp/build --depth 1 --branch 1.0.11; \
	cd /tmp/build; \
	git checkout 0cf4210a66d82377bf0d8350d84c658385db614f; \

	# install electrumx
	mv server lib electrumx_server.py /usr/local/bin/; \

	# clean up
	apt-get purge -y $BUILD_DEPS; \
	apt-get autoremove -y; \
	rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*; \
}

VOLUME /data/electrumx-server

COPY start_electrumx_server /usr/local/bin/
CMD ["start_electrumx_server"]
