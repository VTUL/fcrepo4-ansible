# Fedora 4 Ansible

This is the GitHub repo of the Ansible script for [Fedora 4](https://github.com/fcrepo4/fcrepo4) and related software for a production environment. 

## Requirements

* [Ansible](https://www.ansible.com/)

## Usage


## In this Ansible script

* Ubuntu 14.04 64-bit machine with: 
  * [Tomcat 7](https://tomcat.apache.org) at [http://localhost:8080](http://localhost:8080)
    * Manager username = "fedora4", password = "fedora4"
  * [Fedora 4.5.1](https://wiki.duraspace.org/display/FF/Downloads) at [http://localhost:8080/fcrepo](http://localhost:8080/fcrepo)
    * No authentication configured
  * [Solr 4.10.3](https://lucene.apache.org/solr/) at [http://localhost:8080/solr](http://localhost:8080/solr), for indexing & searching your content.
    * Installed in "/usr/local/tomcat7/solr"
  * [Apache Karaf 4.0.5](http://karaf.apache.org/)
    Installed in /opt/karaf
    Installed as a service apache-karaf
  * [Fuseki 2.3.1](https://jena.apache.org/documentation/serving_data/index.html) at [http://localhost:8080/fuseki](http://localhost:8080/fuseki), for querying and updating.
    * Installed in "/usr/local/fuseki"
    * Dataset Path name "/test"
    * Persistent storage "/usr/local/fuseki/databases/test_data"
  * [Fcrepo-camel-toolbox 4.5.1](https://github.com/fcrepo4-labs/fcrepo-camel-toolbox)
    * Installed in Tomcat container

## Maintainers

Current maintainers:
* [Paul Mather](https://github.com/pmather)
* [Yinlin Chen](https://github.com/yinlinchen)

