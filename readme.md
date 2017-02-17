# Fedora 4 Ansible

This is the GitHub repo of the Ansible script for [Fedora 4](https://github.com/fcrepo4/fcrepo4). 

## Usage
To install the chosen application from scratch on a server using the current local configuration file settings, do the following:

```
cd /path/to/fcrepo4-ansible
vagrant up
```

To install to AWS, do the following:

```
cd /path/to/fcrepo4-ansible
vagrant up --provider aws
```

If using Ansible directly, you will need the IP address of the server you plan to provision. Execute the following command:

```
cd /path/to/fcrepo4-ansible/ansible
ansible-playbook --limit [ip address] site.yml -b
```

### Fedora Configuration

* Default is Fedora 4.7.1 with leveldb.
* To install different Fedora 4 version or other configurations, see [fedora4 role readme](ansible/roles/fedora4)
* To install Fedora 4 with PostgreSQL database, see config example [postgres_site_vars.yml](ansible/postgres_site_vars.yml)  

Installation
------------

These scripts are intended to be run on a Unix-like system. They are tested to work on Mac OSX.

To use these scripts, [Vagrant](https://www.vagrantup.com/) must already have been installed on the local system with the [VirtualBox](http://www.virtualbox.org) provider working. For provisioning to AWS, the `aws` provider must also be installed. This can be done by executing the following command, which will install the `aws` Vagrant provider plugin: `vagrant plugin install vagrant-aws`.

You will need version 1.6+ of [Vagrant](https://vagrantup.com) installed on the local system.

A version of  [Ansible](https://ansible.com) at least 2.1+ must be installed on the local system.

Ansible is easily installed via [Homebrew](http://brew.sh) on Mac OSX via the following command:

```
brew install ansible
```

Finally, these install scripts must be installed on the local machine. This is most easily done by cloning the repository containing them.

```
git clone https://github.com/VTUL/fcrepo4-ansible.git
```

Configuration
-------------

For all environments, a deployment settings file needs to be created in the `ansible/` directory. This file is called `site_vars.yml`:


#### Local files

Sometimes, local files may be supplied during the deployment or provisioning of a target application. A local modeshape repository.json file maybe supply by placing it in the `local_files` directory. The contents of the `local_files` directory hierarchy are subject to `.gitignore` and so won't be checked in via Git accidentally.


### AWS

When using the `aws` provider to `vagrant up` it is necessary to define several environment variables in order to authenticate to AWS and supply a keypair with which Vagrant can log in to the new AWS EC2 instance being deployed.  These environment variables are as follows:

- `KEYPAIR_NAME`: the name of the AWS keypair that will be used to log in to the instance. This keypair should already exist within your AWS account and its private key file should reside on the local system.
- `KEYPAIR_FILE`: the pathname of the private key on the local system corresponding to the aforementioned keypair.
- `AWS_ACCESS_KEY`: the AWS IAM access key to the account under which the EC2 instance will be created.
- `AWS_SECRET_KEY`: the AWS IAM secret key to the account under which the EC2 instance will be created.
- `AWS_SECURITY_GROUPS`: a space-separated list of existing AWS security groups to apply to this instance. (If `AWS_SECURITY_GROUPS` is not set then a default security group is used.)

WARNING: Many of the other AWS EC2 instance settings (e.g., instance type) are set directly in the `Vagrantfile` and make sense only for VTUL users. Please check these are appropriate before bringing up the instance with Vagrant and edit where necessary beforehand. Note that for the `aws` provider to work the AWS account should have a default VPC defined in it.

### Local VM

In the case of the plain `vagrant up` option, a VM will be brought up and configured in the current directory. The application is accessible on the local machine from a Web browser.

You can use `vagrant ssh` to log in to this VM when it is up. When logged out of the VM, `vagrant halt` can be used to shut down the VM. The command `vagrant destroy` will destroy it entirely, requiring another `vagrant up` to recreate it.

Several ports in the running VM are made accessible on the local machine.
Accessing the local port in a Web browser will actually result in the forwarded
port being accessed on the VM. These ports are as follows:

Local | VM   | Description
----- | ---- | -----------
8888  | 8080 | Tomcat (if applicable)

### AWS

For the `vagrant up --provider aws` option, a server running the application will be provisioned in AWS. After a while, it should be possible to log in to this machine via SSH:

```
vagrant ssh
```

The installation and setup of the application and associated software could take quite a while. Its progress will be logged to the screen during the execution of `vagrant up --provider aws`.

When installation is complete and services are running, you can access the application via this URL: `https://$SERVER_HOSTNAME`, where `$SERVER_HOSTNAME` is the hostname of the AWS instance just deployed.  This can be determined by running the following command in the installation scripts directory:

```
vagrant ssh-config | grep HostName | awk '{print $2}'
```

Vagrant commands such as `halt` and `destroy` behave analogously on the AWS instance as they do for local Vagrant VMs.

### Ansible

When using Ansible to provision directly, the playbook will be executed on the server whose IP address is given as `IP`. When the playbook finishes with no failures, the server is accessible at this URL:

```
http://[IP]
```

or

```
https://[IP]
```

### Postgres

Currently, only local databases are supported.  Thus, ensure `project_db_host` does not point to a remote PostgreSQL server.

## Maintainers

Current maintainers:
* [Paul Mather](https://github.com/pmather)
* [Yinlin Chen](https://github.com/yinlinchen)

