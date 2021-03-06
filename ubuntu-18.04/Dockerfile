# This image is an Ubuntu base image for Source-to-Image builds
FROM ubuntu:18.04
MAINTAINER Chris Gooding <chris.j5dev@gmail.com>

LABEL \
      # Location of the STI scripts inside the image.
      io.openshift.s2i.scripts-url=image:///usr/libexec/s2i

ENV \
    # Path to be used in other layers to place s2i scripts into
    STI_SCRIPTS_PATH=/usr/libexec/s2i \
    # The $HOME is not set by default, but some applications needs this variable
    HOME=/opt/app-root/src \
    PATH=/opt/app-root/src/bin:/opt/app-root/bin:$PATH

RUN cat /etc/apt/sources.list

ENV TZ 'Etc/UTC'
RUN echo $TZ > /etc/timezone \
    && apt-get -yq update \
    && apt-get install -yq tzdata locales \
    && rm /etc/localtime \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime \
    && dpkg-reconfigure -f noninteractive tzdata \
    && locale-gen en_GB.utf8

ENV LANG en_GB.utf8
ENV LANGUAGE en_GB.utf8
ENV LC_ALL en_GB.utf8

# This is the list of basic dependencies that all language Docker images can consume.
RUN export DEBIAN_FRONTEND=noninteractive \
    && apt-get -yq update \
    && apt-get -yq install build-essential bzip2 git libcurl4-gnutls-dev libxml2-dev libxslt1-dev libgnutls28-dev gnupg patch procps tar unzip wget zlib1g-dev \
    && apt-get clean

# Setup the 'openshift' user that is used for build execution and for the
# application runtime execution.
RUN useradd -u 1001 -r -g 0 -d ${HOME} -s /sbin/nologin \
-c "Default Application User" default \
&& mkdir -p /opt/app-root \
&& chown -R 1001:0 /opt/app-root

# Copy executable utilities.
COPY bin/ /usr/bin/

# Directory with the sources is set as the working directory so all STI scripts
# can execute relative to this path.
WORKDIR ${HOME}

ENTRYPOINT ["container-entrypoint"]
CMD ["base-usage"]

