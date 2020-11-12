#base image
FROM centos7.4-cuda9.0-cudnn7.0-base-inspur:latest
MAINTAINER wjy

# start install python3
## python version
ARG python=3.6.6
ENV PYTHON_VERSION=${python} 

RUN yum clean packages \
	&& yum -y install wget make zlib zlib-devel bzip2-devel openssl-devel sqlite-devel readline-devel gdbm-devel  gcc libffi-devel \ 
	&& cd /home \
	&& wget https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tgz \
	&& tar -zxvf Python-${PYTHON_VERSION}.tgz \
	&& cd Python-${PYTHON_VERSION} \
	&& ./configure \
	&& make \
	&& make install \
	&& make clean \
	&& rm -rf Python-${PYTHON_VERSION}

## set python3
RUN set -ex \
	&& if [ -e /usr/bin/python ]; then mv /usr/bin/python /usr/bin/python27; fi \
    && if [ -e /usr/bin/pip ]; then mv /usr/bin/pip /usr/bin/pip-python27; fi \
    && ln -s /usr/local/bin/python3 /usr/bin/python \
    && ln -s /usr/local/bin/pip3 /usr/bin/pip

# modify configuration
RUN set -ex \
    && sed -i "s#/usr/bin/python#/usr/bin/python2.7#" /usr/bin/yum \
    && if [ -e /usr/libexec/urlgrabber-ext-down ]; then sed -i "s#/usr/bin/python#/usr/bin/python2.7#" /usr/libexec/urlgrabber-ext-down; fi
# end install python3

# install tensorflow、opencv、keras and numpy
RUN set -ex \
	&& pip install --upgrade pip \
	&& pip install tensorflow-gpu==1.8.0 --user --default-timeout=10000 --ignore-installed --upgrade \
	&& pip install opencv-python==3.4.3.18 --user --default-timeout=10000 --ignore-installed --upgrade \
	&& yum install -y libSM libXrender libXext --setopt=protected_multilib=false \
	&& pip install Keras==2.2.4 --user --default-timeout=10000 --ignore-installed --upgrade \
	&& pip install numpy==1.15.4 --user --default-timeout=10000

# install openssh and openssl
RUN set -ex \
	&& yum install -y openssh-clients openssh-server openssl \
	&& mkdir -p /var/run/sshd \
	&& /usr/bin/ssh-keygen -A
    
# Allow OpenSSH to talk to containers without asking for confirmation
RUN set -ex \
	&& cat /etc/ssh/ssh_config | grep -v StrictHostKeyChecking > /etc/ssh/ssh_config.new \
	&& echo "    StrictHostKeyChecking no" >> /etc/ssh/ssh_config.new \
	&& cat /etc/ssh/sshd_config | grep -v  PermitRootLogin> /etc/ssh/sshd_config.new \
	&& echo "PermitRootLogin yes" >> /etc/ssh/sshd_config.new \
	&& mv /etc/ssh/ssh_config.new /etc/ssh/ssh_config \
	&& mv /etc/ssh/sshd_config.new /etc/ssh/sshd_config

# install jupyterlab                                                                                                                                        
RUN set -ex \
	&& pip install --upgrade pip \
	&& pip --no-cache-dir install jupyterlab \
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