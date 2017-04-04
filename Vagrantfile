# -*- mode: ruby -*-
# vi: set ft=ruby :

ENABLE_DOCKER_GM = false

Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "debian/jessie64"
  config.ssh.insert_key = false 
  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"

  config.vm.define "ansible" do |ansible|
    ansible.vm.hostname = "ansible"
    ansible.vm.network "private_network", ip: "192.168.33.10"

    ansible.vm.provider "virtualbox" do |vb|
      vb.memory = "256"
    end

    ansible.vm.provision "shell", inline: <<-SHELL
      echo "Updating system.."
      apt-get update > /dev/null 2>&1 &&  apt-get upgrade -y > /dev/null 2>&1
      echo "install packages.."
      apt-get install -y build-essential libssl-dev libffi-dev python-dev > /dev/null 2>&1
      echo "install pip.."
      wget --quiet --no-check-certificate https://bootstrap.pypa.io/get-pip.py -O - | python > /dev/null 2>&1
      echo "install ansible.. "
      pip install ansible > /dev/null 2>&1
      echo "add insecure key.."
      wget --quiet --no-check-certificate https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant -O /home/vagrant/.ssh/id_rsa
      wget --quiet --no-check-certificate https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant.pub -O /home/vagrant/.ssh/id_rsa.pub
      chown vagrant /home/vagrant/.ssh/id_rsa*
      chgrp vagrant /home/vagrant/.ssh/id_rsa*
      chmod 600 /home/vagrant/.ssh/id_rsa*
      echo "update host file.."
      echo "192.168.33.20  docker-gm" >> /etc/hosts
      echo "192.168.33.21  dockerhost" >> /etc/hosts
    SHELL
  end

  if ENABLE_DOCKER_GM
  config.vm.define "docker-gm" do |docker|
    docker.vm.hostname = "docker-gm"
    docker.vm.network "private_network", ip: "192.168.33.20"
    docker.vm.provision "shell", inline: <<-SHELL
      echo "Updating system.."
      apt-get update > /dev/null 2>&1 &&  apt-get upgrade -y > /dev/null 2>&1
      echo "setup repo.."
      apt-get install -y apt-transport-https ca-certificates software-properties-common curl > /dev/null 2>&1 
      curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add - > /dev/null 2>&1
      add-apt-repository \
         "deb [arch=amd64] https://download.docker.com/linux/debian \
         $(lsb_release -cs) \
         stable" > /dev/null 2>&1
      echo "install docker.."
      apt-get update > /dev/null 2>&1 && apt-get install -y docker-ce > /dev/null 2>&1
      echo "add vagrant to docker group"
      usermod -aG docker vagrant
    SHELL
  end
  end

  config.vm.define "dockerhost" do |docker|
    docker.vm.hostname = "dockerhost"
    docker.vm.network "private_network", ip: "192.168.33.21"
  end

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  #apt-get update & apt-get upgrade
  #wget --no-check-certificate https://bootstrap.pypa.io/get-pip.py -O - | sudo python
  #apt-get install build-essential python-dev
  #http://docs.ansible.com/ansible/intro_installation.html#latest-releases-via-apt-debian

  # http://www.iannoble.co.uk/upgrade-ansible-2-2-1-debian-8-7/
  # sudo apt-get update && sudo apt-get upgrade
  # sudo apt-get install build-essential libssl-dev libffi-dev python-dev
  # sudo pip install cryptography --upgrade
  # wget --no-check-certificate https://bootstrap.pypa.io/get-pip.py -O - | sudo python
  # sudo pip install ansible --upgrade
end
