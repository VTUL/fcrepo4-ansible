# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

VAGRANTFILE_API_VERSION = "2"

def security_groups( env_var )
  s = ENV[ env_var ]
  if s.nil?
    # Return some default
    case env_var
      when "AWS_SECURITY_GROUPS" then ["default_vpc_web_vt_ssh"]
      else                            []
    end
  else
    s.split
  end
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  # config.vm.box = "ubuntu/trusty64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 8080, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
  
  # Ansible settings
  config.vm.provision :ansible do |ansible|
    ansible.playbook = "ansible/fcrepo4.yml"
    ansible.verbose = ""
  end

  # Application server
  config.vm.define :fcrepo4 do |fcrepo4|
    fcrepo4.vm.hostname = "fcrepo4"

    fcrepo4.vm.provider :virtualbox do |vb, override|
      override.vm.box = "ubuntu/trusty64"
      vb.memory = 4096
      vb.cpus = 2
      # Forward Tomcat/Fedora port in VM to port 8080 on local machine
      override.vm.network :forwarded_port, host: 8080, guest: 8080
    end

    fcrepo4.vm.provider :openstack do |os, override|
      keypair = "#{ENV['KEYPAIR_NAME']}"
      keypair_filename = "#{ENV['KEYPAIR_FILE']}"
      # OpenStack authentication information
      os.openstack_auth_url = "#{ENV['OS_AUTH_URL']}/tokens"
      os.username     = "#{ENV['OS_USERNAME']}"
      os.password     = "#{ENV['OS_PASSWORD']}"
      os.tenant_name  = "#{ENV['OS_TENANT_NAME']}"
      os.region       = "#{ENV['OS_REGION_NAME']}"
      override.ssh.username = "cc"
      os.keypair_name = keypair # as stored in Nova
      override.ssh.private_key_path = "#{keypair_filename}"
      # OpenStack image information
      os.flavor       = "m1.medium"
      os.image        = "Ubuntu-Server-14.04-LTS"
      os.security_groups = security_groups('OS_SECURITY_GROUPS')
      os.floating_ip  = "#{ENV['OS_FLOATING_IP']}"
      os.server_name  = 'fcrepo4'
    end

    fcrepo4.vm.provider :aws do |aws, override|
      keypair = "#{ENV['KEYPAIR_NAME']}"
      keypair_filename = "#{ENV['KEYPAIR_FILE']}"
      override.vm.box = "aws_dummy"
      override.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"
      override.vm.box_check_update = false
      override.nfs.functional = false
      aws.access_key_id = ENV['AWS_ACCESS_KEY']
      aws.secret_access_key = ENV['AWS_SECRET_KEY']
      aws.keypair_name = keypair
      aws.ami = "ami-e7ab8a9c" # Ubuntu Trusty LTS
      aws.region = "us-east-1"
      aws.instance_type = "m3.large"
      aws.security_groups = security_groups('AWS_SECURITY_GROUPS')
      override.ssh.username = "ubuntu"
      override.ssh.private_key_path = "#{keypair_filename}"
      aws.tags = {
        'Name' => 'fcrepo4'
      }
    end

  end

end
