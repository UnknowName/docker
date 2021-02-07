FROM debian:10
ENV TZ=Asia/Shanghai

RUN export VERSION=1.10.5 \
    && apt-get update \
    && apt-get install -y wget subversion gnupg2 wget lsb-release\ 
    && wget https://files.freeswitch.org/repo/deb/debian-release/fsstretch-archive-keyring.asc \
    && apt-key add fsstretch-archive-keyring.asc \
    # Add FreeSWITCH apt
    && echo "deb http://files.freeswitch.org/repo/deb/debian-release/ `lsb_release -sc` main" > /etc/apt/sources.list.d/fs.list \
    && echo "deb-src http://files.freeswitch.org/repo/deb/debian-release/ `lsb_release -sc` main" >> /etc/apt/sources.list.d/fs.list \
    # Add MySQL  odbc apt
    # && echo "deb http://repo.mysql.com/apt/debian/ `lsb_release -sc` mysql-apt-config" > /etc/apt/sources.list.d/mysql.list \
    # && echo "deb http://repo.mysql.com/apt/debian/ `lsb_release -sc` mysql-8.0" >> /etc/apt/sources.list.d/mysql.list \
    # && echo "deb http://repo.mysql.com/apt/debian/ `lsb_release -sc` mysql-tools" >> /etc/apt/sources.list.d/mysql.list \
    # && echo "deb-src http://repo.mysql.com/apt/debian/ `lsb_release -sc` mysql-8.0" >> /etc/apt/sources.list.d/mysql.list \
    && echo "deb http://ftp.de.debian.org/debian sid main" >> /etc/apt/sources.list.d/mysql.list \
    && echo "deb http://security.debian.org/debian-security jessie/updates main" >> /etc/apt/sources.list.d/mysql.list \
    && apt-get update \
    # ODBC MySQL Support
    && apt-get install -y libmysqlclient18 mysql-community-client libmyodbc  unixodbc\
    # apt-cache search PackageName
    && wget https://files.freeswitch.org/releases/freeswitch/freeswitch-${VERSION}.-release.tar.gz \
    && apt-get -y build-dep freeswitch \
    && tar -zxvf freeswitch-${VERSION}.-release.tar.gz -C /usr/src \
    && rm -rf freeswitch-${VERSION}.-release.tar.gz \
    && cd /usr/src/freeswitch-${VERSION}.-release \
    && ./configure --enable-optimization --enable-64 --enable-core-odbc-support --with-odbc \
    && make \
    && make install \
    && make cd-sounds-install \
    && ln -s /usr/local/freeswitch/bin/freeswitch  /usr/bin/freeswitch \
    && ln -s /usr/local/freeswitch/bin/fs_cli      /usr/bin/fs_cli \
    && ln -s /usr/local/freeswitch/conf/freeswitch /etc/freeswitch \
    # Disable IPv6
    && mv /etc/freeswitch/sip_profiles/external-ipv6.xml /etc/freeswitch/sip_profiles/external-ipv6.xml.bak \
    && mv /etc/freeswitch/sip_profiles/internal-ipv6.xml /etc/freeswitch/sip_profiles/internal-ipv6.xml.bak \
    && sed -i "s/::/0.0.0.0/g" /etc/freeswitch/autoload_configs/event_socket.conf.xml \
    && rm -rf  /usr/src/freeswitch-${VERSION}.-release \
    && apt-get clean
COPY ./docker-entrypoint.sh
ENTRYPOINT ["/docker-entrypoint.sh"]