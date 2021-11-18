#
# This file is part of Docker scripts for the Data Hub System.
# Copyright (C) 2021 INPE.
#
# Docker scripts for the Data Hub System is free software; you can redistribute it and/or modify it
# under the terms of the MIT License; see LICENSE file for more details.
#

#
# This Dockerfile is based on CentOS 7
#
ARG BASE_IMAGE=centos:7.8.2003
FROM ${BASE_IMAGE}

LABEL br.inpe.big.maintainer="BIG-INPE"
LABEL br.inpe.big.title="CentOS Base Image for DHuS"
LABEL br.inpe.big.description="Prepares all the \
requirements for installing the Data Hub System on CentOS."


#
# Update all installed packages to their latest versions
#
RUN yum update -y && \
    yum install -y unzip


#
# Install JDK 8
#
ADD software/jdk-8u202-linux-x64.rpm /tmp/

RUN rpm -ivh /tmp/jdk-8u202-linux-x64.rpm && \
    rm -f /tmp/jdk-8u202-linux-x64.rpm

#
# Install DHuS
#

# Version and installation directory
ARG DHUS_VERSION="2.0.0-osf"
ARG DHUS_INSTALLER_NAME="dhus-software-2.0.0-osf-distribution.zip"
ARG DHUS_INSTALLER_URL="https://github.com/SentinelDataHub/dhus-distribution/releases/download/2.0.0-osf/dhus-software-2.0.0-osf-distribution.zip"
ARG DHUS_INSTALLER_DIR="/tmp/"
ARG DHUS_INSTALL_PATH="/opt/dhus/${DHUS_VERSION}"

# Run DHuS under a selected user-id
ARG DHUS_USER_NAME=dhus
ARG DHUS_USER_ID=1000
ARG DHUS_USER_GROUP=dhus
ARG DHUS_USER_GROUP_ID=1000

# Set environment variables
ENV JAVA_HOME=/usr/java/jdk1.8.0_202-amd64
ENV DHUS_DIR=${DHUS_INSTALL_PATH}

# Create a user and group to run DHuS
RUN groupadd --gid ${DHUS_USER_GROUP_ID} ${DHUS_USER_GROUP} && \
    useradd --create-home \
            --shell /bin/bash \
            --uid ${DHUS_USER_ID} \
            --gid ${DHUS_USER_GROUP_ID} ${DHUS_USER_NAME}

# Create DHuS install directory and make it the working directory
RUN mkdir -p ${DHUS_DIR} && \
    chown ${DHUS_USER_NAME}:${DHUS_USER_GROUP} ${DHUS_DIR} 

# Include DHuS distribution in the image layer and unzip it in DHUS_DIR 
ADD ${DHUS_INSTALLER_URL} /tmp/

RUN chown ${DHUS_USER_NAME}:${DHUS_USER_GROUP} /tmp/${DHUS_INSTALLER_NAME}

USER ${DHUS_USER_NAME}

RUN unzip /tmp/${DHUS_INSTALLER_NAME} -d ${DHUS_DIR} && \
    rm -f /tmp/${DHUS_INSTALLER_NAME}


#
# Add PostgreSQL JDBC driver
#
ARG DHUS_PG_JAR_NAME="postgresql-9.4.1212.jar"
ARG DHUS_PG_JAR_URL="https://jdbc.postgresql.org/download/${DHUS_PG_JAR_NAME}"

ADD ${DHUS_PG_JAR_URL} ${DHUS_DIR}/lib/

USER root

RUN chown ${DHUS_USER_NAME}:${DHUS_USER_GROUP} ${DHUS_DIR}/lib/${DHUS_PG_JAR_NAME} && \
    chmod 664 ${DHUS_DIR}/lib/${DHUS_PG_JAR_NAME}


#
# Set the current dir and user
#
WORKDIR ${DHUS_DIR}

USER ${DHUS_USER_NAME}

#
# Start DHuS when the container runs
#
CMD [ "/bin/bash", "start.sh" ]
