# encoding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
    config.vm.box = "fasmat/ubuntu2204-desktop"

   #config.vm.provision "shell", path: "script.sh"
    
    config.vm.hostname = "user-user"

    config.disksize.size = "30GB"

    config.vm.provision "shell", inline: <<-'SHELL'

      sed -i 's/^#* *\(PermitRootLogin\)\(.*\)$/\1 yes/' /etc/ssh/sshd_config
      sed -i 's/^#* *\(PasswordAuthentication\)\(.*\)$/\1 yes/' /etc/ssh/sshd_config
      systemctl restart sshd.service
      echo -e "vagrant\nvagrant" | (passwd vagrant)
      echo -e "root\nroot" | (passwd root)
    SHELL

    config.vm.provider "virtualbox" do |v|
      v.name = "test"
      v.gui = true
      v.memory = 2048
      v.cpus = 2
    end

    config.vm.provision :shell, path: "bootstrap.sh"
    config.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true
    config.vm.network "public_network", ip: "192.168.0.108"

    config.vm.provision "shell", inline: <<-SHELL
      export DEBIAN_FRONTEND=noninteractive
      apt-get update
      apt-get install -y git
      apt-get autoremove -y
      sudo chmod -R 777 /var/www/html
    SHELL

    config.vm.synced_folder 'v-root', '/vagrant'
  end


