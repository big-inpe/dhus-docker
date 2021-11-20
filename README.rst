..
    This file is part of Docker scripts for the Data Hub System.
    Copyright (C) 2021 INPE.

    Docker scripts for the Data Hub System is free software; you can redistribute it and/or modify it
    under the terms of the MIT License; see LICENSE file for more details.


Docker scripts for the Data Hub System
======================================


.. image:: https://img.shields.io/badge/license-MIT-green
        :target: https://github.com/big-inpe/dhus-docker/blob/master/LICENSE
        :alt: Software License


.. image:: https://img.shields.io/badge/lifecycle-experimental-orange.svg
        :target: https://www.tidyverse.org/lifecycle/#experimental
        :alt: Software Life Cycle


About
-----


The `Data Hub System <http://sentineldatahub.github.io/DataHubSystem/>`_ (DHuS) is a Java web based system designed to manage the on-line dissemination of ESA Copernicus Sentinels data. Please refer to `Serco and Gael Systems web page <http://sentineldatahub.github.io/DataHubSystem/>`_.


This repository contains `Docker <https://docs.docker.com/>`_ and `Docker Compose <https://docs.docker.com/compose/>`_ scripts to help deploying a single DHuS instance.


Dependencies
------------


In order to get DHuS up and running you should have `Docker <https://docs.docker.com/>`_ and `Docker Compose <https://docs.docker.com/compose/>`_ installed. Please, refer to the official documentation sections `Install Docker Engine <https://docs.docker.com/engine/install/>`_ and `Install Docker Compose <https://docs.docker.com/compose/install/>`_ if you do not have them installed.


Besides Docker and Docker Compose, you will need to download the `jdk-8u202-linux-x64.rpm <https://www.oracle.com/br/java/technologies/javase/javase8-archive-downloads.html#license-lightbox>`_.


DHuS up and running
-------------------


**1.** Clone this repository::

    git clone https://github.com/big-inpe/dhus-docker.git
    

**2.** Change the current directory to the place you cloned the repository::

    cd dhus-docker    


**3.** Create an environment file named ``.env-dhus`` based on the ``.env-dhus-template``.


**4.** Make sure to create all the directories for the variables defined in ``.env-dhus``::

    DHUS_CONFIG_DIR=./data/config
    DHUS_DATA_DIR=./data/dhus
    DHUS_LOG_DIR=./data/log
    NGINX_LOG_DIR=./data/log
    PG_DATA_DIR=./data/pg


**5.** Create a configuration file named ``default.conf`` in the ``niginx/config`` directory. There is a template file named ``default.conf-template`` in ``niginx/config`` directory that you can base yours.


**6.** Create a directory named ``software`` under ``dhus``. The ``Dockerfile`` under ``dhus`` will reference the JDK downloaded file (``jdk-8u202-linux-x64.rpm``) in that folder (``dhus/software``).


**7.** In the ``dhus/config`` directory there are two configuration files for DHuS: ``dhus-internal-db.xml`` and ``dhus-pg.xml``. If you want to store records in an internal Java database, you can copy the ``dhus-internal-db.xml`` to a new file named ``dhus.xml`` under the directory pointed by ``DHUS_CONFIG_DIR`` in the ``.env-dhus`` file. If you want to use PostgreSQL to record the metadata, please, copy the ``dhus-pg.xml`` to ``dhus.xml`` under the directory pointed by ``DHUS_CONFIG_DIR`` in the ``.env-dhus`` file. Copy the ``start.sh`` and ``log4j2.xml`` files also and add any changes you want.


**8.** In the ``dhus.xml`` there are some important configuration options:

- The ``server:external`` entry can be configured as ``<server:external host="127.0.0.1" path="/" port="443" protocol="https"/>``.

- If you are going to synchronize data usng the DHuS instance, the ``system:executor`` entry should be enabled: ``<system:executor enabled="true" batchModeEnabled="false" />``.

- If you are using PostgreSQL, remember to set the ``user`` and ``password`` in the attribute ``JDBCUrl`` in the XML element ``system:database``: ``<system:database ... JDBCUrl="jdbc:postgresql://dhus-db:5432/dhus" login="postgres" password="secret"/>``.


**9.** Create and start the containers with Docker Compose:


**a)** If you have selected the internal Java database option, use a command such as::

    docker-compose -f docker-compose-internal-db.yml --env-file .env-dhus up -d


**b)** If you have selected the PostgreSQL option, you need to follow some extra steps:


- **b.1.** Create the container database with::

    docker-compose --env-file .env-dhus up --detach db


- **b.2.** Create a temporary container based on DHuS image to create the tables required by DHuS in PostgreSQL and run the ``updateDatabase.sh`` script::

    docker run -it --rm --name dhus --network dhus_net inpe/dhus:2.7.8-osf  bash

    ./updateDatabase.sh "org.hsqldb.jdbcDriver" "jdbc:postgresql://dhus-db:5432/dhus" "postgres" "secret"


- **b.3.** Finally, launch all the other containers::

    docker-compose --env-file .env-dhus up --detach


Testing the Environment
-----------------------


If the last command in the previous section worked successfully, you can access the DHuS UI in your browser::

    firefox https://127.0.0.1/


.. tip::

    In DHuS, the default admin credentials is login ``root`` and password ``password``.