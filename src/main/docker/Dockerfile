FROM confluentinc/cp-zookeeper:6.2.0

ENV COMPONENT=zookeeper \
    ZK_USER=mrzookeeper \
    ZK_GROUP=onap

USER root

RUN userdel -r appuser && groupadd $ZK_GROUP && useradd $ZK_USER -u 1000 -G 1000,$ZK_GROUP

WORKDIR /home/$ZK_USER

COPY include/etc/confluent/docker/* /etc/confluent/docker/
RUN chmod -R +x /etc/confluent/docker \
&& chown -R $ZK_USER:$ZK_GROUP /var/lib/${COMPONENT} /etc/kafka /etc/confluent/docker /var/log/kafka /var/lib/kafka /var/log/confluent

USER $ZK_USER

EXPOSE 2181 2888 3888

CMD ["/etc/confluent/docker/run"]