## An Introduction to Vagrant

王洪城

2014.12

@huhulab


##

### What's wrong with development environment

* it can take too long to set it up
* it can differ too much from testing and production development
* Each developer has a differnt one.
	+ Easily out of sync with standards
	+ Many versions of softwares or packages
	+ It works on my machine


### What is Vagrant

> ...a **developer-friendly interface** for Virtualbox, VMWare...

> Create and configure lightweight, reproducible, and portable development environments automately


### What can we get

* Command line tool
* Lowers set up time
* Fully control entire environments and versions
* Eliminates "works on my machine" excuse
* Environment per object
* Shared across the team
* Cooperate with other tools
	+ Providers like VirtualBox, VMWare, AWS
	+ Provisions like Chef, Puppet, Ansible, Shell


### Git of the devlopment clouds

* For Individual
	+ Maintain consistency across multiple projects
	+ Can run multiple environments on a single host machine.
	+ Easily tear down and rebuild
* For Team
	+ Indentical developemnt environments.
	+ Consistent and portable
* For Company
	+ Easier onboarding of new talent
	+ Build once and distribute to teams


##

### Minimum requirements

* VirtualBox
* Ruby
* Vagrant


### Quick Start

* $ vagrant box add {title} {url}
	+ find the avaliable boxes from http://www.vagrantbox.es/
* $ vagrant init title
* $ vagrant up
* $ vagrant ssh
	
	
### Vagrantfile

* Simple Ruby code which typically contains a Vagrant configuration block
* First thing loaded by Vagrant
* Basic file created when 'init' is called from within a directory
* Add more options for more configuration
* Are portable and meant to be placed under version controll

### Boxes

* Many base boxes avaliable over the Internet
* you can create your own
	+ $ vagrant package \--base {title} \--output {new title}
* you can list your base boxes
	+ $ vagrant box list
* you can remove base boxes
	+ $ vagrant box remove {box name} {provider}


### Up

* Vagrant runs the virtual machines headless by default
* Vagrant create a new virtual machine based on the base box
* set the visible name of virtual machine
* Manage the network interfaces
* Finally, boots the machine


### SSH

* Vagrant makes SSH to the virtual amchine easy from the project directory
* Project files are avaliable in the VM at '/vagrant'
* The VM has both read and write access to the shared folder
* Gain root sccess use "sudo"


##

### Shared Folders

* First shared folder is '/vagrant'
* Add shared Folder in vagrantfile
* Options: create, disabled, owner, group, type 

```
config.vm.synced_folder "host folder", "VM folder", disable: True
```

   
   
### NFS

* the default shared folder implementations have high performance penalties 

```
config.vm.synced_folder ".", "/vagrant", type: "nfs"
```


##
 
### Networking

* Forwarded Ports
* Host-Only Networking
* Bridged Networking


### Forwarded Ports

* Basic Usage
* Collision Detection and Correction
* Choose protocol TCP or UDP

		config.vm.forwarded_port 80, 8080, protocol: "udp", auto_correct: True
		config.vm.usable_port_range = (2200..2250)

* Pros
	+ simple to set up
	+ accessible from outside computer
* Cons
	+ Explicit about every port
	+ Any computer on local network can access your computer
	+ Can not forword to port leses than 1024 on the host system
	

### Host-Only Networking

* Specify a static IP for the VM
* basic usage

		config.vm.network "hostonly", "192.168.33.10"
* Pros
	+ The network is secure
	+ Multiple VMs can communicate with each other and host
	+ The Vms can also communicate with the host itself
* Cons
	+ the network is isolated from coworkers
 
 
### Bridged Networking

* Use DHCP to assign a IP to the VM
* Basic usage

		config.vm.network "bridged"
* Pros
	+ Have a IP and no isolation
* Cons
	+ can not specify a static IP
	

### Composing Networking Options
* Vagrant allows you to enable multiple network options

		config.vm.forwarded_port 80, 8080
		config.vm.network "hostonly", "192.168.33.10"
		config.vm.network "bridged"


## 

### multiple machine

 * Quickly establish a network of multi-machine environment, such as a web server, db server
 * Establish a distributed system, learn how they interact
 * Test communications between API and other components
 *  Disaster simulation, like network disconnection, the machine crashes, connection timeouts, etc.
 


### defining multiple machine

```
Vagrant.configure("2") do |config|
	config.vm.box = "precise64"

	config.vm.define "web" do |web| 
		web.vm.forward_port 80, 8080
		web.vm.provision :shell, path: "provision.sh"
	end

	config.vm.define "db" do |db| 
		# fill this in the same way.
	end 
end
```

### Controlling Multiple Machines

* Most commands, such as up, destroy, and reload now take an argument with the name of the machine to affect
* specify multiple targets in the same command
* or use a regular expression

```
$ vagrant reload web
$ vagrant status
$ vagrant reload node1 node2 node3
$ vagrant reload /node\d/
```


### Communication Between Machines

* define a host-only network on the machines

```
Vagrant.configure("2") do |config|
	config.vm.box = "precise64"

	config.vm.define "web" do |web|
		web.vm.forward_port 80, 8080
		web.vm.provision :shell, path: "provision.sh"
		web.vm.network :hostonly, "192.168.33.10"
	end
	
	config.vm.define "db" do |db|
		db.vm.network :hostonly, "192.168.33.11"
	end
	
end
```


##

### Provisions

* automatically install software, alter configurations, and more on the machine as part of the vagrant up process

* Shell, Ansible, Chef, Puppet
* when vagrant up, provisioning is run
* use --provision to force provisioing or --no-provision crossly
* --provision-with to run a specify provisioner


### provisioning with Shell

```
#!/usr/bin/env bash
echo "Installing Apache and setting it up..."
apt-get update >/dev/null 2>&1
apt-get install -y apache2 >/dev/null 2>&1
rm -rf /var/www
ln -fs /vagrant /var/www


config.vm.provision "shell", path: "provision.sh"

```

### provisioning with Ansible

* Inventory File
	+ generate by vagrant
	+ specify a inventory resource
* playbook

```
---
- hosts: all
  	sudo: yes
 	vars:
    whoami: "{{ lookup('env', 'USER') }}"
    user_home: "{{ lookup('env', 'HOME') }}"
  tasks:
  	- name: install mysql
  	  apt: pkg={{item}} state=present
      with_items:
        - mysql-server
        - mysql-client
```

* running ansible

```
Vagrant.configure("2") do |config|
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
  end
end
```


### why use Ansible

* Shell script is simple
* Ansible can be run multiple times safely
* Absible can easily be run against multiple servers
* Ansible can target certain servers easily


## Some pitfalls

* [static file refresh error when using Appache and Nginx](http://segmentfault.com/blog/fenbox/1190000000264347)
* [file permission error](http://serverfault.com/questions/398414/vagrant-set-default-share-permissions)
* [cann not map vagrant project with VM](http://type.so/linux/vagrant-challenges.html)

## FAQ
 
* [Where is Vagrant saving changes to the VM?](http://stackoverflow.com/questions/8225820/where-is-vagrant-saving-changes-to-the-vm/8331810#8331810)
* [Should I use Vagrant or Docker.io for creating an isolated environment?](http://stackoverflow.com/questions/16647069/should-i-use-vagrant-or-docker-io-for-creating-an-isolated-environment/21314566#21314566)
* [Vagrant error : Failed to mount folders in Linux guest](http://stackoverflow.com/questions/22717428/vagrant-error-failed-to-mount-folders-in-linux-guest/23752848#23752848)
* [How to connect to Mysql Server inside VirtualBox Vagrant?](http://stackoverflow.com/questions/10709334/how-to-connect-to-mysql-server-inside-virtualbox-vagrant/10794530#10794530)