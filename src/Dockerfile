# zhihu_spider
# version 0.0.1

FROM centos

MAINTAINER "ruoyu.liu" <lrysjtu@gmail.com>

# install Chinese support
# RUN yum -y groupinstall chinese-support
# ENV LANG "zh_CN.UTF-8"

# install something common
RUN yum install -y gcc gcc-c++ git make python-devel postgresql-devel wget vim libffi-devel libxml2-devel libxslt-devel openssl-devel && yum clean all

# update libs
RUN yum -y swap -- remove systemd-container systemd-container-libs -- install systemd systemd-libs
RUN yum -y update; yum clean all; \
(cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;

# install pip
RUN wget https://bootstrap.pypa.io/get-pip.py;python get-pip.py;rm -f get-pip.py

# install mongodb
RUN rpm --import https://www.mongodb.org/static/pgp/server-3.2.asc
ADD mongodb-enterprise.repo /etc/yum.repos.d/mongodb-enterprise.repo 
RUN yum install -y mongodb-enterprise
# you need map a directory to /data/db to start mongod

# install rabbitmq
# its too slow to download while building
#RUN wget http://www.rabbitmq.com/releases/erlang/erlang-18.1-1.el7.centos.x86_64.rpm
#RUN wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.1/rabbitmq-server-3.6.1-1.noarch.rpm
ADD erlang-18.1-1.el7.centos.x86_64.rpm /tmp/erlang-18.1-1.el7.centos.x86_64.rpm
ADD rabbitmq-server-3.6.1-1.noarch.rpm /tmp/rabbitmq-server-3.6.1-1.noarch.rpm
RUN rpm --import https://www.rabbitmq.com/rabbitmq-signing-key-public.asc
RUN cd /tmp && yum install -y erlang-18.1-1.el7.centos.x86_64.rpm && yum install -y rabbitmq-server-3.6.1-1.noarch.rpm
#RUM rm -f erlang-18.1-1.el7.centos.x86_64.rpm
#RUM rm -f rabbitmq-server-3.6.1-1.noarch.rpm

# install zhihu_spider requirements
RUN pip install wheel Scrapy pymongo requests celery -i http://pypi.douban.com/simple --trusted-host pypi.douban.com

EXPOSE 27017 15672

ENV container docker

# for celery worker run as root user
ENV C_FORCE_ROOT true

VOLUME [ "/sys/fs/cgroup" ]

CMD ["/usr/sbin/init"]
