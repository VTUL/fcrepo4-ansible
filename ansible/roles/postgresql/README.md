PostgreSQL
=========

Installs PostgreSQL from the official repository, and configures it.

Requirements
------------

Apt must be installed, along with our Common role.

Role Variables
--------------

The role variable below determines whether the PostgreSQL server used is to be installed and run locally on this server or whether it already exists on a remote server:

- `postgresql_is_local`: true if `project_db_host` indicates the PostgreSQL host is on localhost or a Unix socket; false otherwise
