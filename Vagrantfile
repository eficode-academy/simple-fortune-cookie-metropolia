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
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/jammy64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # NOTE: This will enable public access to the opened port
  # Forward all ports that are used in training material
  for i in 8000..9000
    config.vm.network "forwarded_port", guest: i, host: i
  end
  config.vm.network "forwarded_port", guest: 3000, host: 3000
  config.vm.network "forwarded_port", guest: 3001, host: 3001

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  #config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder ".", "/home/vagrant/project"

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

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
     apt-get update
     # install docker-ce
     apt-get install -y lsb-release ca-certificates apt-transport-https software-properties-common
     curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
     echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
     apt-get update
     apt-get install -y docker-ce
     usermod -a -G docker vagrant
     # install docker-compose
     if ! command -v docker-compose >/dev/null 2>&1; then
      echo "docker-compose is not installed."
      mkdir -p /home/vagrant/.docker/cli-plugins/
      TMPFILE=$(mktemp)
      http_code=$(curl -SL -w "%{http_code}" https://github.com/docker/compose/releases/download/v2.3.3/docker-compose-linux-x86_64 -o "$TMPFILE")
      [[ "$http_code" == "200" ]] || { rm "$TMPFILE"; echo "Curl failed with $http_code"; exit 1; }
      mv "$TMPFILE" /home/vagrant/.docker/cli-plugins/docker-compose
      chmod +x /home/vagrant/.docker/cli-plugins/docker-compose
      ln -s /home/vagrant/.docker/cli-plugins/docker-compose /usr/local/bin/docker-compose 
     fi
     # setup needed items for jenkins
     cp /home/vagrant/project/ci/sshd_config /etc/ssh/sshd_config # don't do this in real environment
     systemctl restart ssh
     apt-get install -y openjdk-11-jdk
     mkdir -p /var/jenkins
     chown -R vagrant:vagrant /var/jenkins
  SHELL
end
