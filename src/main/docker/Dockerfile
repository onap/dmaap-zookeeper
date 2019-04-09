FROM ubuntu:16.04

ENV ZK_USER=mrzookeeper \
    ZK_DATA_DIR=/var/lib/zookeeper/data \
    ZK_LOG_DIR=/var/log/zookeeper \
    JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64


ARG ZK_DIST=zookeeper-3.4.9

RUN set -x \
    && apt-get update \
    && apt-get install -y openjdk-8-jre-headless wget netcat-openbsd \
    && wget -q "http://archive.apache.org/dist/zookeeper/$ZK_DIST/$ZK_DIST.tar.gz" \
    && wget -q "http://archive.apache.org/dist/zookeeper/$ZK_DIST/$ZK_DIST.tar.gz.asc" \
   && tar -xzf "$ZK_DIST.tar.gz" -C /opt \
    && rm -r  "$ZK_DIST.tar.gz" "$ZK_DIST.tar.gz.asc" \
    && ln -s /opt/$ZK_DIST /opt/zookeeper \
    && rm -rf /opt/zookeeper/CHANGES.txt \
    /opt/zookeeper/README.txt \
    /opt/zookeeper/NOTICE.txt \
    /opt/zookeeper/CHANGES.txt \
    /opt/zookeeper/README_packaging.txt \
    /opt/zookeeper/build.xml \
    /opt/zookeeper/config \
    /opt/zookeeper/contrib \
    /opt/zookeeper/dist-maven \
    /opt/zookeeper/docs \
    /opt/zookeeper/ivy.xml \
    /opt/zookeeper/ivysettings.xml \
    /opt/zookeeper/recipes \
    /opt/zookeeper/src \
    /opt/zookeeper/$ZK_DIST.jar.asc \
    /opt/zookeeper/$ZK_DIST.jar.md5 \
    /opt/zookeeper/$ZK_DIST.jar.sha1 \
    && apt-get autoremove -y wget \
    && rm -rf /var/lib/apt/lists/*

# Copy configuration generator script to bin
COPY scripts /opt/zookeeper/bin/

# Create a user for the zookeeper process and configure file system ownership
# for necessary directories and symlink the distribution as a user executable
RUN set -x \
    && mkdir -p $ZK_DATA_DIR  $ZK_LOG_DIR /usr/share/zookeeper /tmp/zookeeper /usr/etc/ \
    && ln -s /opt/zookeeper/conf/ /usr/etc/zookeeper \
    && ln -s /opt/zookeeper/bin/* /usr/bin \
    && ln -s /opt/zookeeper/$ZK_DIST.jar /usr/share/zookeeper/ \
&& ln -s /opt/zookeeper/lib/* /usr/share/zookeeper

RUN chmod -R 777 /opt/zookeeper/bin
ENTRYPOINT /opt/zookeeper/bin/start-zookeeper.sh
EXPOSE 2181 2888 3888

RUN useradd $ZK_USER \
    && [ `id -u $ZK_USER` -eq 1000 ] \
    && [ `id -g $ZK_USER` -eq 1000 ] \
    && chown -R $ZK_USER:$ZK_USER /opt/$ZK_DIST/ /opt/zookeeper/ /var/lib/ /var/log/ /tmp/zookeeper/

USER $ZK_USER


