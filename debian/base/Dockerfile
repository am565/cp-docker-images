#
# Copyright 2016 Confluent Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM registry.redhat.io/ubi8/ubi:8.1-328

ARG COMMIT_ID=unknown
LABEL io.confluent.docker.git.id=$COMMIT_ID
ARG BUILD_NUMBER=-1
LABEL io.confluent.docker.build.number=$BUILD_NUMBER

ARG CONFLUENT_PACKAGES_REPO=$CONFLUENT_PACKAGES_REPO

ARG ALLOW_UNSIGNED=false
#Set an env var so that it's available in derived images
ENV ALLOW_UNSIGNED=$ALLOW_UNSIGNED

MAINTAINER partner-support@confluent.io
LABEL io.confluent.docker=true

# Python
# ENV PYTHON_VERSION="2.7.9-1"
# ENV PYTHON_PIP_VERSION="8.1.2"

# Confluent
ENV SCALA_VERSION="2.12"

ARG KAFKA_VERSION=$KAFKA_VERSION
ARG CONFLUENT_MAJOR_VERSION=$CONFLUENT_MAJOR_VERSION
ARG CONFLUENT_MINOR_VERSION=$CONFLUENT_MINOR_VERSION
ARG CONFLUENT_PATCH_VERSION=$CONFLUENT_PATCH_VERSION

ARG CONFLUENT_MVN_LABEL=$CONFLUENT_MVN_LABEL
ARG CONFLUENT_PLATFORM_LABEL=$CONFLUENT_PLATFORM_LABEL

ENV KAFKA_VERSION=$KAFKA_VERSION
ENV CONFLUENT_MAJOR_VERSION=$CONFLUENT_MAJOR_VERSION
ENV CONFLUENT_MINOR_VERSION=$CONFLUENT_MINOR_VERSION
ENV CONFLUENT_PATCH_VERSION=$CONFLUENT_PATCH_VERSION

ENV CONFLUENT_MVN_LABEL=$CONFLUENT_MVN_LABEL
ENV CONFLUENT_PLATFORM_LABEL=$CONFLUENT_PLATFORM_LABEL

ENV CONFLUENT_VERSION="$CONFLUENT_MAJOR_VERSION.$CONFLUENT_MINOR_VERSION.$CONFLUENT_PATCH_VERSION"
ENV CONFLUENT_DEB_VERSION="1"

# Zulu
#ENV ZULU_OPENJDK_VERSION="8=8.38.0.13"
ENV ZULU_OPENJDK_VERSION="8"

# This affects how strings in Java class files are interpreted.  We want UTF-8 and this is the only locale in the
# base image that supports it
ENV LANG="C.UTF-8"
COPY confluent-docker-utils/ /tmp/
RUN ls -l /tmp
RUN echo "===> Updating debian ....." \
    # TODO debian jessie has been deprecated and is only ex
    # && yum update \
    && echo "===> Installing curl wget netcat python...." \
    && yum install -y \
                curl \
                gnupg2 \
                git \
                wget \
                nmap-ncat \
                python3 \
                python3-pip \
    && echo "===> Installing python packages ..."  \
    && pip3 install --no-cache-dir /tmp/confluent-docker-utils/ \
    && yum remove -y git \
    && echo "Installing Zulu OpenJDK ${ZULU_OPENJDK_VERSION}" \
    && rpm --import http://repos.azulsystems.com/RPM-GPG-KEY-azulsystems \
    && curl -o /etc/yum.repos.d/zulu.repo http://repos.azulsystems.com/rhel/zulu.repo \
    && yum install -y zulu-${ZULU_OPENJDK_VERSION} \
    && echo "===> Installing Kerberos Patch ..." \
    && yum install -y krb5-workstation krb5-devel \
    && yum clean all \
    && rpm --import http://packages.confluent.io/rpm/3.0/archive.key
COPY confluent-dist.repo /etc/yum.repos.d/
COPY confluent.repo /etc/yum.repos.d/
    # && echo "===> Adding confluent repository...${CONFLUENT_PACKAGES_REPO}/deb/${CONFLUENT_MAJOR_VERSION}.${CONFLUENT_MINOR_VERSION}" \
    # && if [ "x$ALLOW_UNSIGNED" = "xtrue" ]; then echo "APT::Get::AllowUnauthenticated \"true\";" > /etc/apt/apt.conf.d/allow_unauthenticated; else curl -L ${CONFLUENT_PACKAGES_REPO}/deb/${CONFLUENT_MAJOR_VERSION}.${CONFLUENT_MINOR_VERSION}/archive.key | apt-key add - ; fi \
    # && echo "deb [arch=amd64] ${CONFLUENT_PACKAGES_REPO}/deb/${CONFLUENT_MAJOR_VERSION}.${CONFLUENT_MINOR_VERSION} stable main" >> /etc/apt/sources.list

ENV CUB_CLASSPATH=/etc/confluent/docker/docker-utils.jar
COPY include/etc/confluent/docker /etc/confluent/docker
