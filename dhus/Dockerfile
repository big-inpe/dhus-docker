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

LABEL br.inpe.big.maintainer="BIG-INPE" \
      br.inpe.big.title="CentOS Base Image for DHuS" \
      br.inpe.big.description="Prepares all the requirements for installing the Data Hub System on CentOS"


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
# Set environment variables
#
ENV JAVA_HOME=/usr/java/jdk1.8.0_202-amd64
ENV DHUS_DIR=/opt/dhus/2.0.0-1-osf


#
# Run DHuS with under a selected user-id
#
ARG DHUS_USER_NAME=dhus
ARG DHUS_USER_ID=1000
ARG DHUS_USER_GROUP=dhus
ARG DHUS_USER_GROUP_ID=1000

RUN groupadd --gid ${DHUS_USER_GROUP_ID} ${DHUS_USER_GROUP} && \
    useradd --create-home \
            --shell /bin/bash \
            --uid ${DHUS_USER_ID} \
            --gid ${DHUS_USER_GROUP_ID} ${DHUS_USER_NAME}


#
# Create DHuS install directory and make it the working directory
#
RUN mkdir -p ${DHUS_DIR} && \
    chown ${DHUS_USER_NAME}:${DHUS_USER_GROUP} ${DHUS_DIR} 

WORKDIR ${DHUS_DIR}


#
# Make the user that DHuS should run the active one
#
USER ${DHUS_USER_NAME}


#
# Include DHuS distribution in the image layer and unzip it in DHUS_DIR 
#
ADD software/dhus-software-2.0.0-1-osf-distribution.zip /tmp/

RUN unzip /tmp/dhus-software-2.0.0-1-osf-distribution.zip -d ${DHUS_DIR}

USER root

RUN rm -f /tmp/dhus-software-2.0.0-1-osf-distribution.zip


#
# Add PostgreSQL jar
#
ADD software/postgresql-9.4.1212.jar ${DHUS_DIR}/lib/

RUN chown ${DHUS_USER_NAME}:${DHUS_USER_GROUP} ${DHUS_DIR}/lib/postgresql-9.4.1212.jar && \
    chmod 644 ${DHUS_DIR}/lib/postgresql-9.4.1212.jar

USER ${DHUS_USER_NAME}


#
# Start DHuS when the container runs
#
CMD [ "/bin/bash", "start.sh" ]
