FROM python:3-slim-stretch

RUN { set -ex; \
	apt-get update; \
	apt-get install --no-install-recommends -y ca-certificates libleveldb1v5 gosu; \
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
	git clone https://github.com/kyuupichan/electrumx.git /tmp/build --depth 1 --branch 1.2; \
	cd /tmp/build; \
	git checkout 1da8cb547d733fe3da9924a0d31187a6d5bb58a1; \

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
