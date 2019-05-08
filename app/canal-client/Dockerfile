FROM anapsix/alpine-java

MAINTAINER Longjianghu <215241062@qq.com>

RUN mkdir /canal/config -p

COPY ./src/canal_client.jar /canal/
COPY ./src/config/* /canal/config/
COPY ./docker-entrypoint /usr/local/bin/docker-entrypoint.sh

WORKDIR /canal

RUN chmod 755 /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["docker-entrypoint.sh"]

CMD ["/bin/sh"]