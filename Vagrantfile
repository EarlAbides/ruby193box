# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/xenial64"
  config.ssh.forward_agent = true

  config.vm.synced_folder "", "/home/ubuntu/chef-repo"
  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

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
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    # vb.gui = true

    # Customize the amount of memory on the VM:
    vb.memory = "3120"
  end

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
  # To get the AWS keys onto the VM, we will hack them in based on the
  # current user environment. by The default .bashrc for the ubuntu image we
  # are using will source .bash_aliases if it exists.
  aws_key = ENV['AWS_ACCESS_KEY_ID']
  aws_secret = ENV['AWS_SECRET_ACCESS_KEY']
  aws_region = ENV['AWS_REGION']

  config.vm.provision 'shell', privileged: true, inline: <<-SHELL
  # https://github.com/rbenv/ruby-build/wiki#suggested-build-environment
    apt-get install -y gcc autoconf bison build-essential
    apt-get install -y libssl-dev libyaml-dev libreadline6-dev zlib1g-dev
    apt-get install -y libncurses5-dev libffi-dev libgdbm3 libgdbm-dev
  SHELL

  config.vm.provision 'shell', privileged: false, inline: <<-SHELL
    # download rbenv if the directory is missing
    RBENV_VERSION=6cd487db8138816d9c2898846e
    if cd ~/.rbenv && git rev-parse --verify --short=26 HEAD | grep -q $RBENV_VERSION; then
      echo 'rbenv already downloaded at expected revision'
    else
      git clone https://github.com/rbenv/rbenv.git ~/.rbenv
      cd ~/.rbenv && git checkout $RBENV_VERSION
    fi
    # download ruby-build if the directory is missing
    RBBUILD_VERSION=59607cbe9b060be701c992a86a
    if cd ~/.rbenv/plugins/ruby-build && git rev-parse --verify --short=26 HEAD | grep -q $RBBUILD_VERSION; then
      echo 'ruby-build already downloaded at expected revision'
    else
      git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
      cd ~/.rbenv/plugins/ruby-build && git checkout $RBBUILD_VERSION
    fi
    RBENV_PATH='export PATH="$HOME/.rbenv/bin:$PATH"'
    if grep -q "$RBENV_PATH" ~/.bash_profile ; then
      echo 'rbenv path already in .bash_profile'
    else
      echo $RBENV_PATH >> ~/.bash_profile
    fi
    RBENV_INIT='eval "$(rbenv init -)"'
    if grep -q "$RBENV_INIT" ~/.bash_profile ; then
      echo 'rbenv init already in .bash_profile'
    else
      echo $RBENV_INIT >> ~/.bash_profile
    fi
  SHELL

  config.vm.provision 'shell', privileged: false, inline: <<-SHELL
    rbenv install 1.9.3-p551
    rbenv global 1.9.3-p551
  SHELL

  config.vm.provision 'shell', privileged: false, inline: <<-SHELL
    gem install bundler
    rbenv rehash
    cd /home/ubuntu/chef-repo
    bundle install
  SHELL
end
