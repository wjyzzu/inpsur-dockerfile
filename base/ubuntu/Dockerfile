# base image
FROM ubuntu:18.04

MAINTAINER wjy

# install openssh and openssl 
RUN set -ex \
	&& apt-get -o Acquire::Check-Valid-Until=false  -o Acquire::Check-Date=false   update \
	&& apt-get install -y --no-install-recommends openssh-client openssh-server \
	&& mkdir -p /var/run/sshd

# Allow OpenSSH to talk to containers without asking for confirmation
RUN set -ex \
	&& cat /etc/ssh/ssh_config | grep -v StrictHostKeyChecking > /etc/ssh/ssh_config.new \
	&& echo "    StrictHostKeyChecking no" >> /etc/ssh/ssh_config.new \
	&& cat /etc/ssh/sshd_config | grep -v  PermitRootLogin> /etc/ssh/sshd_config.new \
	&& echo "PermitRootLogin yes" >> /etc/ssh/sshd_config.new \
	&& mv /etc/ssh/ssh_config.new /etc/ssh/ssh_config \
	&& mv /etc/ssh/sshd_config.new /etc/ssh/sshd_config

# start install python3
## python version
ARG python=3.6.11
ENV PYTHON_VERSION=${python}

# start install python3
## python version
ARG python=3.6.11
ENV PYTHON_VERSION=${python} 

RUN sed -ex \
	&& apt-get -o Acquire::Check-Valid-Until=false  -o Acquire::Check-Date=false   update \
	&& apt-get install -y --allow-downgrades --allow-change-held-packages --no-install-recommends \
	&& apt-get install -y wget build-essential libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libreadline-dev libffi-dev zlib1g-dev libsqlite3-dev \
	&& cd /home \
	&& wget https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tgz \
	&& tar -zxvf Python-${PYTHON_VERSION}.tgz \
	&& cd Python-${PYTHON_VERSION} \
	&& ./configure \
	&& make \
	&& make install \
	&& make clean \
	&& rm -rf Python-${PYTHON_VERSION} 	 

## install python3-pip
RUN set -ex \
	&& apt-get install -y python3-pip \
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/* 

## set python3
RUN set -ex \
	&& if [ -e /usr/bin/python ]; then mv /usr/bin/python /usr/bin/python27; fi \
    && if [ -e /usr/bin/pip ]; then mv /usr/bin/pip /usr/bin/pip-python27; fi \
    && ln -s /usr/local/bin/python3 /usr/bin/python \
    && ln -s /usr/local/bin/pip3 /usr/bin/pip

# end install python3

# install jupyterlab                                                                                                                                       
RUN set -ex \
	&& pip install --upgrade pip \
	&& pip --no-cache-dir install jupyter jupyterlab \
	&& rm -rf /root/.cache/pip/http/*

## configure jupyterlab                                                                                                                                             
RUN set -ex \
	&& mkdir /etc/jupyter/ \
	&& wget -P /etc/jupyter/  https://raw.githubusercontent.com/Winowang/jupyter_gpu/master/jupyter_notebook_config.py \
	&& wget -P /etc/jupyter/ https://raw.githubusercontent.com/Winowang/jupyter_gpu/master/custom.js 

## tini
ENV TINI_VERSION v0.18.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini