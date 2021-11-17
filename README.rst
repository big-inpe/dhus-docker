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


Besides Docker and Docker Compose, you will need to download the following softwares:

- dhus-software-2.0.0-1-osf-distribution.zip

- jdk-8u202-linux-x64.rpm

- postgresql-9.4.1212.jar



DHuS up and running
-------------------


**1.** Clone this repository::

    git clone https://github.com/big-inpe/dhus-docker.git
    

**2.** Change the current directory to the place you cloned the repository::

    cd dhus-docker    


**3.** Create an environment file named ``.env-dhus`` based on the ``.env-dhus-template``.


**4.** Create a configuration file named ``default.conf`` in the ``niginx/config`` directory. There is a template file name ``default.conf-template`` in ``niginx/config`` directory that you can base yours.


**5.** Create a directory named ``software`` under ``dhus``. The ``Dockerfile`` under ``dhus`` will reference the three downloaded files: ``dhus-software-2.0.0-1-osf-distribution.zip``, ``jdk-8u202-linux-x64.rpm`` and ``postgresql-9.4.1212.jar`` in that folder (``dhus/software``).


**6.** In the ``dhus/config`` directory there are two configuration files for DHuS: ``dhus-internal-db.xml`` and ``dhus-pg.xml``. If you want to store records in an internal Java database, you can copy the ``dhus-internal-db.xml`` to a new file named ``dhus.xml``. If you want to use PostgreSQL to record the metadata, please, copy the ``dhus-pg.xml`` to ``dhus.xml``.


**7.** In the ``dhus.xml`` there are some important configuration options:

- The ``server:external`` entry can be configured as ``<server:external host="127.0.0.1" path="/" port="443" protocol="https"/>``.

- If you are goingto synchronize data, the ``system:executor`` entry should be enabled: ``<system:executor enabled="true" batchModeEnabled="false" />``.


**8.** Create and start the containers with Docker Compose:


**8.1.** If you have selected the PostgreSQL option, use a command such as::

    docker-compose --env-file .env-dhus up --detach


**8.2.** If you have selected the internal Java database option, use a command such as::

    docker-compose -f docker-compose-internal-db.yml --env-file .env-dhus up -d


Testing the Environment
-----------------------


If the last command in the previous section worked successfully, you can access the DHuS UI in your browser::

    firefox https://127.0.0.1/

