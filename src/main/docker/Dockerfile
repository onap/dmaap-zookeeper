FROM confluentinc/cp-base:5.3.1

ENV COMPONENT=zookeeper \
    ZK_USER=mrzookeeper

RUN echo "===> installing ${COMPONENT}..." \
    && wget -qO - http://packages.confluent.io/deb/3.0/archive.key | apt-key add - \
    && echo "deb [arch=amd64] http://packages.confluent.io/deb/3.0 stable main" | tee -a /etc/apt/sources.list \
    && apt-key update && apt-get update && apt-get install -y confluent-kafka-2.11 --force-yes \
    && echo "===> clean up ..."  \
    && apt-get autoremove -y && apt-get clean && rm -rf /tmp/* /var/lib/apt/lists/* \
    && echo "===> clean up ..."  \
    && apt-get clean && rm -rf /tmp/* /var/lib/apt/lists/* \
    && echo "===> Setting up ${COMPONENT} dirs" \
    && mkdir -p /var/lib/${COMPONENT}/data  /var/lib/${COMPONENT}/log /etc/${COMPONENT}/secrets/jaas /etc/${COMPONENT}/data  /var/log/kafka /var/log/confluent  \
    && chmod -R ag+w /etc/kafka /var/lib/${COMPONENT}/data /var/lib/${COMPONENT}/log  /etc/${COMPONENT}/secrets /etc/${COMPONENT}/data  /var/log/kafka /var/log/confluent \
    && chown -R root:root /var/log/kafka /var/log/confluent /var/lib/kafka /var/lib/zookeeper  
    
RUN set -x \
    && apt-get update \
    && apt-get install -y git --force-yes \
    && git clone -b master --single-branch http://gerrit.onap.org/r/dmaap/messagerouter/messageservice.git /tmp/zookeeper/gerrit

COPY include/etc/confluent/docker /etc/confluent/docker
RUN chmod -R +x /etc/confluent/docker

RUN useradd  -u 1000  -g 0 $ZK_USER
RUN  chown -R $ZK_USER:0  /tmp/zookeeper
USER $ZK_USER

EXPOSE 2181 2888 3888

CMD ["/etc/confluent/docker/run"]