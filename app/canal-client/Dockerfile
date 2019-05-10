FROM anapsix/alpine-java

MAINTAINER Longjianghu <215241062@qq.com>

# 更换国内源
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories && apk update

ENV TIMEZONE=Asia/Shanghai

RUN set -xe \
	&& apk add --no-cache --virtual tzdata \
	&& ln -snf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone \
    && rm /var/cache/apk/* -rf

COPY ./src/canal_client.jar /canal/
COPY ./src/config/* /canal/config/
COPY ./docker-entrypoint /usr/local/bin/docker-entrypoint.sh

WORKDIR /canal

RUN chmod 755 /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["docker-entrypoint.sh"]

CMD ["/bin/sh"]