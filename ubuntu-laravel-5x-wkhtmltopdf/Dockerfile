FROM j5dev/s2i-ubuntu-laravel5x:latest

MAINTAINER Chris Gooding <chrisg@refero.uk>

USER root

RUN apt-add-repository -y "deb http://security.ubuntu.com/ubuntu xenial-security main" && \
    apt-get -yq update && \
    apt-get install -y libxrender1 libfontconfig1 libx11-dev libjpeg62 libxtst6 \
                           fontconfig xfonts-75dpi xfonts-base libpng12-0 && \
    wget "https://downloads.wkhtmltopdf.org/0.12/0.12.5/wkhtmltox_0.12.5-1.xenial_amd64.deb" && \
    dpkg -i wkhtmltox_0.12.5-1.xenial_amd64.deb && \
    apt-get -f install && \
    apt-get -y autoremove && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Directory with the sources is set as the working directory so all STI scripts
# can execute relative to this path.
WORKDIR ${HOME}

# Drop root privileges for running
USER 1001
CMD ["base-usage"]