FROM ubuntu:20.04
USER root
SHELL ["/bin/bash", "-c"]

# 设置环境变量
ENV HOME_DIR /root/workspace/
ENV RAILS_ENV development
ENV NLS_LANG "SIMPLIFIED CHINESE_CHINA.AL32UTF8"
ENV LD_LIBRARY_PATH /opt/oracle/instantclient_11_2
ENV TNS_ADMIN /opt/oracle/instantclient_11_2
ENV RVM_HOME /usr/share/rvm

# 设置容器里的工作目录
WORKDIR $HOME_DIR

COPY ./sources.list ./sources.list

# 重新安装，解决更换国内源后Certificate verification failed问题
RUN apt-get update && apt-get install -y openssl && apt-get install -y ca-certificates --reinstall

# 安装依赖、设置时区、安装jemalloc、配置oracle环境
RUN mv /etc/apt/sources.list /etc/apt/sources.list.bak && mv ./sources.list /etc/apt/sources.list &&\
    ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && echo 'Asia/Shanghai' >/etc/timezone &&\
    apt-get update && apt-get install -y vim zip tar git curl iputils-ping libpq-dev libaio1 build-essential libjemalloc-dev software-properties-common &&\
    cd /opt/ && curl -O http://rccdevops.oss-cn-hangzhou.aliyuncs.com/oracle.zip && unzip oracle.zip && rm -rf oracle.zip &&\
    cd $HOME_DIR

# jemalloc环境变量
ENV LD_PRELOAD $LD_PRELOAD:/usr/lib/x86_64-linux-gnu/libjemalloc.so

# 安装RVM
RUN apt-add-repository -y ppa:rael-gc/rvm && apt-get update && apt-get install -y rvm && source $RVM_HOME/scripts/rvm &&\
    echo '[[ -s "$RVM_HOME/scripts/rvm" ]] && source "$RVM_HOME/scripts/rvm"' >> ~/.bashrc &&\
    echo "ruby_url=https://cache.ruby-china.com/pub/ruby" > $RVM_HOME/user/db

# RVM环境变量
ENV PATH="$PATH:$RVM_HOME/bin"

# 运行
ENTRYPOINT ["tail", "-f", "/etc/apt/sources.list"]
