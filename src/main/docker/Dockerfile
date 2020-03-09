FROM confluentinc/cp-base:5.3.1

# allow arg override of required env params
ARG KAFKA_ZOOKEEPER_CONNECT
ENV KAFKA_ZOOKEEPER_CONNECT=${KAFKA_ZOOKEEPER_CONNECT}
ARG KAFKA_ADVERTISED_LISTENERS
ENV KAFKA_ADVERTISED_LISTENERS=${KAFKA_ADVERTISED_LISTENERS}

ENV COMPONENT=kafka \
    KAFKA_USER=mrkafka

RUN echo "===> installing ${COMPONENT}..." \
    && apt-get update && apt-get install -y confluent-kafka-${SCALA_VERSION}=${CONFLUENT_VERSION}${CONFLUENT_PLATFORM_LABEL}-${CONFLUENT_DEB_VERSION} \
    \
    && echo "===> clean up ..."  \
    && apt-get clean && rm -rf /tmp/* /var/lib/apt/lists/* \
    \
    && echo "===> Setting up ${COMPONENT} dirs..." \
    && mkdir -p /var/lib/${COMPONENT}/data /etc/${COMPONENT}/secrets/cert /etc/${COMPONENT}/secrets/jaas /etc/${COMPONENT}/data  /var/log/kafka /var/log/confluent \
    && chmod -R ag+w /etc/${COMPONENT} /var/lib/${COMPONENT}/data   /etc/${COMPONENT}/secrets /etc/${COMPONENT}/data  /var/log/kafka /var/log/confluent \
    && chown -R root:root /var/log/kafka /var/log/confluent /var/lib/kafka /var/lib/zookeeper

COPY include/etc/confluent/docker /etc/confluent/docker
RUN chmod -R +x /etc/confluent/docker

COPY org.onap.dmaap.mr.trust.jks \
     org.onap.dmaap.mr.p12 \
     org.onap.dmaap.mr.keyfile \
     /etc/${COMPONENT}/secrets/cert/

COPY  kafka11aaf-jar-with-dependencies.jar /usr/share/java/${COMPONENT}/


RUN useradd  -u 1000  -g 0 $KAFKA_USER
