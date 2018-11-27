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

To install to OpenStack, do the following:

```
cd /path/to/fcrepo4-ansible
vagrant up --provider openstack
```

If using Ansible directly, you will need the IP address of the server you plan to provision. Execute the following command:

```
cd /path/to/fcrepo4-ansible/ansible
ansible-playbook --limit [ip address] site.yml -b
```

### Fedora Configuration

* Default is Fedora 4.7.5 with a file-based database.
* To install different Fedora 4 version or other configurations, see [fedora4 role readme](ansible/roles/fedora4)
* To install Fedora 4 with PostgreSQL database, set `fedora_use_postgresql_modeshape: yes` in `ansible/site_vars.yml` and set other DB-related settings there as needed.  

Installation
------------

These scripts are intended to be run on a Unix-like system. They are tested to work on Mac OSX.

To use these scripts, [Vagrant](https://www.vagrantup.com/) must already have been installed on the local system with the [VirtualBox](http://www.virtualbox.org) provider working. For provisioning to AWS, the `aws` provider must also be installed. This can be done by executing the following command, which will install the `aws` Vagrant provider plugin: `vagrant plugin install vagrant-aws`. To provision to the OpenStack Chameleon Cloud, the openstack provider needs to be installed. It can be installed via the following command: `vagrant plugin install vagrant-openstack-provider`.

You will need version 1.6+ of [Vagrant](https://vagrantup.com) installed on the local system.

A version of [Ansible](https://ansible.com) at least 2.1+ must be installed on the local system.

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

Sometimes, local files may be supplied during the deployment or provisioning of a target application. A local modeshape repository.json file may be supplied by placing it in the `local_files` directory. The contents of the `local_files` directory hierarchy are subject to `.gitignore` and so won't be checked in via Git accidentally.


### AWS

When using the `aws` provider to `vagrant up` it is necessary to define several environment variables in order to authenticate to AWS and supply a keypair with which Vagrant can log in to the new AWS EC2 instance being deployed.  These environment variables are as follows:

- `KEYPAIR_NAME`: the name of the AWS keypair that will be used to log in to the instance. This keypair should already exist within your AWS account and its private key file should reside on the local system.
- `KEYPAIR_FILE`: the pathname of the private key on the local system corresponding to the aforementioned keypair.
- `AWS_ACCESS_KEY`: the AWS IAM access key to the account under which the EC2 instance will be created.
- `AWS_SECRET_KEY`: the AWS IAM secret key to the account under which the EC2 instance will be created.
- `AWS_SECURITY_GROUPS`: a space-separated list of existing AWS security groups to apply to this instance. (If `AWS_SECURITY_GROUPS` is not set then a default security group is used.)

WARNING: Many of the other AWS EC2 instance settings (e.g., instance type) are set directly in the `Vagrantfile` and make sense only for VTUL users. Please check these are appropriate before bringing up the instance with Vagrant and edit where necessary beforehand. Note that for the `aws` provider to work the AWS account should have a default VPC defined in it.


### OpenStack

When deploying to the OpenStack Chameleon Cloud, several environment variables must be defined in order to authenticate to OpenStack and define a keypair to be used to log in to the new Chameleon Cloud instance being deployed.  The following environment variables must be defined:

- `KEYPAIR_NAME`: the name of the OpenStack keypair that will be used to log in to the instance. This keypair should already exist within your OpenStack account and its private key file should reside on the local system.
- `KEYPAIR_FILE`: the pathname of the private key on the local system corresponding to the aforementioned keypair.
- `OS_FLOATING_IP`: the floating IP address (as a "dotted quad", i.e., x.x.x.x) to be assigned to this instance. This floating IP must already be available to the OpenStack project under which the instance is being deployed.
- `OS_SECURITY_GROUPS`: a space-separated list of existing OpenStack security groups to apply to this instance. (If `OS_SECURITY_GROUPS` is not set then a default security group is used.)
- `OS_USERNAME`: your OpenStack user name
- `OS_PASSWORD`: your OpenStack login password
- `OS_AUTH_URL`: the URL of the OpenStack endpoint
- `OS_TENANT_NAME`: the ID of your OpenStack Chameleon Cloud project (tenant)
- `OS_REGION_NAME`: the OpenStack region in which you wish to deploy the instance

The `OS_USERNAME`; `OS_PASSWORD`; `OS_AUTH_URL`; `OS_TENANT_NAME`; and `OS_REGION_NAME` settings are most easily set via an OpenStack RC file downloaded via the OpenStack dashboard. To do this, log in to the dashboard and select the "Compute" -> "Access & Security" page. On that page, select the "API Access" tab. Click the "Download OpenStack RC File" to download the RC script to your local system. This is a bash script that sets the aforementioned environment variables when run. The script also prompts the user to enter his or her OpenStack password. The `OS_PASSWORD` environment variable is set to the value entered. You should run this script to define those environment variables prior to deploying via Vagrant, e.g., by executing `. /path/to/OpenStack_RC_File.sh`.


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

### OpenStack

Installation to OpenStack is similar to that of AWS above. After provisioning with `vagrant up --provider openstack` it should be possible to log in to the newly-deployed machine via SSH:

```
vagrant ssh
```

As with the `aws` provider, the application can be accessed via the URL `https://$SERVER_HOSTNAME`, where `$SERVER_HOSTNAME` is the hostname of the OpenStack instance just deployed. You can determine the hostname by using the following command:

```
vagrant ssh-config | grep HostName | awk '{print $2}'
```

### Ansible

When using Ansible to provision directly, the playbook will be executed on the server whose IP address is given as `IP`. When the playbook finishes with no failures, the server is accessible at this URL:

```
http://[IP]:8080/fcrepo/
```

or

```
https://[IP]:8080/fcrepo/
```

### Postgres

Currently, only local databases are tested.  Thus, ensure `project_db_host` does not point to a remote PostgreSQL server.

## Maintainers

Current maintainers:
* [Paul Mather](https://github.com/pmather)
* [Yinlin Chen](https://github.com/yinlinchen)
