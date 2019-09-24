# git helper image
FROM alpine:3.9 as githelper
RUN apk add --no-cache git
# Copy APIKeys
RUN git init /zookeeper/gerrit \
    && cd /zookeeper/gerrit \
    && git remote add origin http://gerrit.onap.org/r/dmaap/messagerouter/messageservice.git \
    && git config core.sparsecheckout true \
    && echo "oom-topics/data-zookeeper/*" >> .git/info/sparse-checkout \
    && git pull --depth=1 origin master

# main image build
FROM docker.io/zookeeper:${zookeeper.dist.version}

ARG ZK_DIST=zookeeper-${zookeeper.dist.version}

ENV ZK_USER=mrzookeeper \
    ZK_DATA_DIR=/var/lib/zookeeper/data \
    ZK_LOG_DIR=/var/log/zookeeper \
    ZK_CONF_DIR=/opt/zookeeper/conf \
    ZK_DIST_VER=$ZK_DIST

# passing config path to upstream image
ENV ZOOCFGDIR=$ZK_CONF_DIR

# copy API-keys from githelper
COPY --from=githelper /zookeeper/gerrit/oom-topics /tmp/zookeeper/gerrit/oom-topics

# Create required directories and configure file system ownership
# for necessary directories and symlink the distribution as a user executable
# and remove unecessary files
RUN mkdir -p \
        /opt \
        /usr/etc \
        /usr/share/zookeeper \
        $ZK_DATA_DIR \
        $ZK_LOG_DIR \
    && ln -snf /$ZK_DIST_VER/ /opt/zookeeper \
    && ln -snf /opt/zookeeper/conf/ /usr/etc/zookeeper \
    && ln -snf /opt/zookeeper/bin/* /usr/bin \
    && ln -snf /opt/zookeeper/$ZK_DIST_VER.jar /usr/share/zookeeper \
    && ln -snf /opt/zookeeper/lib/* /usr/share/zookeeper \
    && rm -rf \
        /conf \
        /home/zookeeper \
        /docker-entrypoint.sh \
        /opt/zookeeper/CHANGES.txt \
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
        /opt/zookeeper/$ZK_DIST_VER.jar.asc \
        /opt/zookeeper/$ZK_DIST_VER.jar.md5 \
        /opt/zookeeper/$ZK_DIST_VER.jar.sha1

# Copy configuration generator script to bin
COPY scripts /opt/zookeeper/bin/
RUN chmod -R 777 /opt/zookeeper/bin

# delete zookeeper user from upsteram image and create custom user instead
RUN deluser zookeeper > /dev/null 2>&1 \
    && { addgroup -S -g 1000 $ZK_USER > /dev/null 2>&1 && adduser -S -D -G $ZK_USER -u 1000 $ZK_USER > /dev/null 2>&1; } \
    || { groupadd -r -g 1000 $ZK_USER && useradd -r -M -g 1000 -u 1000 $ZK_USER; } \
    && chown -R $ZK_USER:$ZK_USER \
        /opt/zookeeper \
        /tmp/zookeeper \
        /var/lib/zookeeper \
        /var/log/zookeeper \
        $ZK_CONF_DIR \
        $ZK_DATA_DIR \
        $ZK_LOG_DIR

EXPOSE 2181 2888 3888
USER $ZK_USER
ENTRYPOINT ["/opt/zookeeper/bin/start-zookeeper.sh"]