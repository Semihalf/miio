FROM ubuntu:18.04

USER root

ENV \
  TITAN_VER=6.4.0 \
  TTCN3_DIR=/opt/titan \
  TMP_DIR=/tmp \
  USER=root

ENV \
  TITAN_SRC_DIR=${TMP_DIR}/titan.core \
  PATH=/usr/lib/ccache:${TTCN3_DIR}/bin:${PATH} \
  LD_LIBRARY_PATH=${TTCN3_DIR}/lib:${LD_LIBRARY_PATH}


ENV \
  ETS=ets \
  CONFIG=config.cfg

ENV \
  DEBIAN_FRONTEND=noninteractive \
  LC_ALL=C.UTF-8 \
  LANG=C.UTF-8


WORKDIR ${TMP_DIR}

RUN \
  apt-get update && \
  apt-get -y install \
    bison \
    expect \
    flex \
    g++ \
    git \
    libncurses5-dev \
    libpcap-dev \
    libssl-dev \
    libxml2-dev \
    make \
    && \
  apt-get -y upgrade && \
  git clone https://github.com/eclipse/titan.core ${TITAN_SRC_DIR} && \
  git -C ${TITAN_SRC_DIR} checkout ${TITAN_VER} && \
  cd ${TITAN_SRC_DIR} && \
  echo TTCN3_DIR := ${TTCN3_DIR} >> Makefile.personal && \
  echo JNI := no >> Makefile.personal && \
  make && \
  find loggerplugins -name Makefile | xargs sed -ie 's/cp /cp -d /' && \
  make install && \
  apt-get -y -o Dpkg::Options::="--force-confnew" install localepurge && sed -ie '/^USE_DPKG/d' /etc/locale.nopurge && localepurge && apt-get -y purge localepurge && \
  apt-get -y autoremove && \
  apt-get clean && \
  echo 'Dir::Cache {srcpkgcache "";pkgcache "";}' > /etc/apt/apt.conf.d/02nocache && rm -f /var/cache/apt/*.bin && \
  find /usr/share/locale -mindepth 1 -maxdepth 1 ! -name 'en' -exec rm -rf {} + && \
  find / -name "*.pyc" -exec rm {} + && \
  rm -rf ${TTCN3_DIR}/man ${TMP_DIR}/* /usr/share/doc/* /usr/share/man/* /usr/share/groff/* /usr/share/info/* /usr/share/lintian/* /usr/share/linda/* /var/cache/man/* /usr/share/i18n/charmaps/* /var/lib/apt/lists/* /var/log/*


WORKDIR /git


ENTRYPOINT ["/opt/titan/bin/ttcn3_start"]
CMD ["\$ETS", "\$CONFIG"]

