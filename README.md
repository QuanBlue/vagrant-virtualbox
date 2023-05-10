# Vagrant-VirtualBox example

## Introduction
### Vagrant
-  Vagrant is written in: **Ruby**

-  It can be used in projects written in other programming languages such as **PHP**,**Python**, **Java**, **C#**, and **JavaScript**

**Vagrant cheat sheet:** [Cheat sheet](https://gist.github.com/wpscholar/a49594e2e2b918f4d0c4)  
**Vagrant machine setting:** [Machine setting](https://www.vagrantup.com/docs/vagrantfile/machine_settings)

### Virtualbox
- VirtualBox is a general-purpose full virtualizer for x86 hardware, targeted at server, desktop and embedded use.
- **Virtualbox manual:** [Manual](https://www.virtualbox.org/manual/ch01.html)

## Create virtual machine with **Vagrant** and **VirtualBox**

-  Create folder contain virtual machine (ex: vagrant_ex)

```
mkdir vagrant_ex
```

- Install plugin

```
vagrant plugin install vagrant-disksize
```

-  Create **Vagrantfile** in this new folder (eg: `vargrant_ex`). Custom **Vagrantfile**

   -  Create virtual machine **ubuntu 22.04** with image [fasmat/ubuntu2204-desktop](https://app.vagrantup.com/fasmat/boxes/ubuntu2204-desktop)

   ```sh
   config.vm.box = "fasmat/ubuntu2204-desktop"
   ```

   -  Disk store virtual machine **30GB**

   ```sh
   config.disksize.size = "30GB"
   ```

   -  Create user for virtual machine **vagrant** and **root**

   ```sh
   config.vm.provision "shell", inline: <<-'SHELL'

     sed -i 's/^#* *\(PermitRootLogin\)\(.*\)$/\1 yes/' /etc/ssh/sshd_config
     sed -i 's/^#* *\(PasswordAuthentication\)\(.*\)$/\1 yes/' /etc/ssh/sshd_config
     systemctl restart sshd.service
     echo -e "vagrant\nvagrant" | (passwd vagrant)
     echo -e "root\nroot" | (passwd root)
   SHELL
   ```

   -  Config virtual machine with **name**, **memory**, **cpu**,....

   ```ruby
   config.vm.provider "virtualbox" do |v|
         v.name = "test"
         v.gui = true
         v.memory = 2048
         v.cpus = 2
       end
   ```

   -  Run shell script in **bootstrap.sh** to install **apache2** on virtual machine

   ```ruby
   config.vm.provision :shell, path: "bootstrap.sh"
   ```

   -  Config network connection with `192.168.1.20` is my ip network

   ```ruby
   config.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true
   config.vm.network "public_network", ip: "192.168.1.20"

   ```

   -  Choose interface for network brigde to

   -  Run command line on new created virtual machine

   ```sh
   config.vm.provision "shell", inline: <<-SHELL
       export DEBIAN_FRONTEND=noninteractive
       apt-get update
       apt-get install -y git
       apt-get autoremove -y
       sudo chmod -R 777 /var/www/html
     SHELL
   ```

   -  Copy folder **./v-root** in local into VM in directory **/vagrant**

   ```ruby
   config.vm.synced_folder 'v-root', '/vagrant'
   ```

## Run

Run terminal command to auto install VM on VirtualBox by Vagrantfile script

```sh
cd vagrant_ex
vagrant up
```

It will automatic `download image`, `install` and `config` our new virtual machine through **Vagrantfile**. When this process done, **VirtualBox** will auto start and run this virtual machine.

After complete install VM, ssh to remote control that VM by CLI

```sh
vagrant ssh
```

Choose internet interface  
![Choose Interface to connect Internet](./Image/chooseInterface.png)

Check sync folder `v-root`  
![check sync folder](./Image/synceFolder.png)

### Login User

> **usr**: root  
> **pwd**: root

> **usr**: vagrant  
> **pwd**: vagrant


## Remove VM and Box

**Remove VM**  
`vagrant global-status` to get all VM and id  
`vagrant destroy <id>` to destroy VM with id

**Remove box**  
`vagrant box list` to get all box  
`vagrant box remove <name>` to remove box with name
