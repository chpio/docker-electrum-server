FROM chpio/gosu:jessie

RUN { \
	set -ex; \

	apt-get update; \
	apt-get install --no-install-recommends -y \
		python python-openssl python-leveldb; \
	rm -rf /var/lib/apt/lists/*; \
}

RUN { \
	set -ex; \

	# add user & group
	groupadd --system --gid 1000 electrum-server; \
	useradd --system --uid 1000 --gid 1000 electrum-server; \

	BUILD_DEPS="git ca-certificates python-setuptools build-essential python-dev libleveldb-dev"; \
	apt-get update; \
	apt-get install --no-install-recommends -y $BUILD_DEPS; \

	# download
	git clone https://github.com/spesmilo/electrum-server.git /tmp/electrum-server; \
	cd /tmp/electrum-server; \
	git checkout 62a593569954bcbe3f9b666188d17c9cf9afef55; \

	python setup.py install; \

	mv src run_electrum_server.py /usr/bin/; \

	# clean up
	apt-get purge -y $BUILD_DEPS; \
	apt-get autoremove -y; \
	rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*; \
}

VOLUME /data/electrum-server

COPY electrum-server.conf /etc/

ENV PYTHON_EGG_CACHE /tmp/.python-eggs

COPY start-electrum-server /usr/bin/
ENTRYPOINT ["start-electrum-server"]
