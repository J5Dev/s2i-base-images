FROM ubuntu:18.04
MAINTAINER Chris Gooding <chris.j5dev@gmail.com>

LABEL \
      # Location of the STI scripts inside the image.
      io.openshift.s2i.scripts-url=image:///usr/libexec/s2i

ENV \
    PHP_VERSION=7.1 \
    PHP_VER_SHORT=71 \
    # Path to be used in other layers to place s2i scripts into
    STI_SCRIPTS_PATH=/usr/libexec/s2i \
    # The $HOME is not set by default, but some applications needs this variable
    HOME=/opt/app-root/src \
    PATH=/opt/app-root/src/bin:/opt/app-root/bin:$PATH

ENV TZ 'Etc/UTC'
RUN echo $TZ > /etc/timezone && \
    apt-get -yq update && \
    apt-get install -yq tzdata locales && \
    rm /etc/localtime && \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata && \
    locale-gen en_GB.utf8

ENV LANG en_GB.utf8
ENV LANGUAGE en_GB.utf8
ENV LC_ALL en_GB.utf8

RUN apt-get install -y nginx curl zip wget gnupg unzip git software-properties-common supervisor sqlite3 && \
    add-apt-repository -y ppa:ondrej/php && \
    apt-get install -y php7.1-fpm php7.1-cli php7.1-mcrypt php7.1-gd php7.1-mysql \
                          php7.1-imap php-memcached php7.1-mbstring php7.1-xml php7.1-curl \
                          php7.1-sqlite3 php7.1-xdebug && \
    php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer && \
    mkdir /run/php && \
    curl -sL https://deb.nodesource.com/setup_10.x -o nodesource_setup.sh && \
    bash nodesource_setup.sh && \
    apt-get install -y nodejs  build-essential libpng-dev && \
    apt-get remove -y --purge software-properties-common && \
    apt-get -y autoremove && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY ./configs/nginx /etc/nginx/
COPY ./configs/php-fpm /etc/php/7.1/fpm/
COPY ./configs/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

EXPOSE 8080
EXPOSE 8443

# Setup the 'openshift' user that is used for build execution and for the
# application runtime execution.
RUN useradd -u 1001 -r -g 0 -d ${HOME} -s /sbin/nologin \
-c "Default Application User" default \
&& mkdir -p /opt/app-root \
&& chown -R 1001:0 /opt/app-root

# Copy the S2I scripts to /usr/libexec/s2i since we set the label that way
COPY ./.s2i/bin/ $STI_SCRIPTS_PATH

# Copy executable utilities.
COPY bin/ /usr/bin/

RUN mkdir /tmp/sessions && \
    mkdir /var/run/supervisor && \
    mkdir /var/run/nginx && \
    mkdir -p /var/lib/nginx && \
    mkdir -p /var/log/nginx && \
    mkdir -p /var/cache/nginx && \
    touch /var/log/nginx/error.log && \
    touch /var/log/nginx/access.log && \
    chown -R 1001:0 /var/log/nginx && \
    chown -R 1001:0 /var/lib/nginx && \
    chown -R 1001:0 /opt/app-root /tmp/sessions && \
    chown -R 1001:0 /var/log/supervisor && \
    chmod -R a+rwx /tmp/sessions && \
    chmod -R a+rwx /var/run/supervisor && \
    chmod -R a+rwx /var/run/nginx && \
    chmod -R a+rwx /var/run/php && \
    chmod -R a+rwx /var/log/nginx && \
    chmod -R a+rwx /var/cache/nginx && \
    chmod -R a+rwx /var/lib/nginx && \
    chmod -R a+rwx /var/log/supervisor && \
    chmod -R ug+rwx /var/log/nginx && \
    chmod -R ug+rwx /var/cache/nginx && \
    chmod -R ug+rwx /var/lib/nginx && \
    chmod -R ug+rwx /opt/app-root && \
    chmod -R ug+rwx /var/log/supervisor && \
    ln -s /var/log/nginx/ `2>&1 nginx -V | grep -oP "(?<=--prefix=)\S+"`/logs && \
    ln -sf /dev/stdout /var/log/nginx/access.log && \
    ln -sf /dev/stderr /var/log/nginx/error.log

# Directory with the sources is set as the working directory so all STI scripts
# can execute relative to this path.
WORKDIR ${HOME}

# Drop root privileges for running
USER 1001
CMD ["base-usage"]
