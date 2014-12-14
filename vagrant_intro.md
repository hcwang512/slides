## An Introduction to Vagrant
\
\
![](/Users/wanghongcheng/Downloads/fig/logo_vagrant.png)
\ 王洪城
\ 2014.12
@huhulab


## What's wrong with development environment
* it can take too long to set it up
* it can differ too much from testing and production development


## What is Vagrant
> ...a **developer-friendly interface** for Virtualbox, VMWare...

![](/Users/wanghongcheng/Downloads/fig/virtualbox_vagrant.jpg)

## Git of the devlopment clouds
* For Individual
* For Team
* For NewComer

## Quickly Start
<section>
<pre><code>
$ vagrant init precise64 http://files.vagrantup.com/precise64.box
A `Vagrantfile` has been placed in this directory. You are now ready to `vagrant up` your first virtual environment! Please read the comments in the Vagrantfile as well as documentation on `vagrantup.com` for more information on using Vagrant.
</code></pre>
</section>
<section>
<pre><code>
vagrant up
    Bringing machine 'default' up with 'virtualbox' provider...
    [default] Box 'precise64' was not found. Fetching box from specified URL for
    the provider 'virtualbox'. Note that if the URL does not have
    a box for this provider, you should interrupt Vagrant now and add
    the box yourself. Otherwise Vagrant will attempt to download the
    full box prior to discovering this error.
    Downloading with Vagrant::Downloaders::HTTP...
    Downloading box: http://files.vagrantup.com/precise64.box
    Extracting box...
    Cleaning up downloaded box...
    Successfully added box 'precise64' with provider 'virtualbox'!
    [default] Importing base box 'precise64'...
    [default] Matching MAC address for NAT networking...
    [default] Setting the name of the VM...
    [default] Clearing any previously set forwarded ports...
    [default] Fixed port collision for 22 => 2222. Now on port 2200.
    [default] Creating shared folders metadata...
    [default] Clearing any previously set network interfaces...
    [default] Preparing network interfaces based on configuration...
    [default] Forwarding ports...
    [default] -- 22 => 2200 (adapter 1)
    [default] Booting VM...
    [default] Waiting for VM to boot. This can take a few minutes.
    [default] VM booted and ready for use!
    [default] Configuring and enabling network interfaces...
    [default] Mounting shared folders...
    [default] -- /vagrant
</code></pre>
</section>

<section>
<pre><code>
$ vagrant ssh
Linux vagrant 3.2.0-4-amd64 #1 SMP Debian 3.2.51-1 x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
You have mail.
Last login: Wed Dec 10 22:43:59 2014 from 10.0.2.2
</code></pre>
</section>

## Boxes
<section>
 1. add a box downloaded from https://vagrantbox.es/
    <pre><code>
    $ vagrant box add hashicorp/precise32
    </code></pre>
    <pre><code>
    Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/precise32"
  end
  </code></pre>
</section>
<section>
2.  add an exsiting vm
<pre><code>
So for instance, if I am interested to repackage VM `Node_default_1387087098` then I will run,

~~~ .sh
vagrant package --base Node_default_1387087098 --output /myfolder/precise64Node.box
</code></pre>

</section>

## Shared Filesystem
<pre><code>
  #Share an additional folder to the guest VM. The first  argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
    config.vm.synced_folder "/Users/wanghongcheng/Documents", "/share"
</code></pre>
   
## NFS
* the default shared folder implementations have high performance penalties 

<pre><code>
Vagrant.configure("2") do |config|
  # ...

  config.vm.synced_folder ".", "/vagrant", type: "nfs"
end
</code></pre>

    
## Networking


* Forwarded Ports
* Host-Only Networking
* Bridged Networking

## Forwarded Ports
* Basic Usage
<pre><code>
 config.vm.forwarded_port 80, 8080
 </code></pre>
 
* Collision Detection and Correction
<pre><code>
config.vm.forwarded_port 80, 8080, auto_correct: true

config.vm.usable_port_range = (2200..2250)
</code></pre>

* TCP or UDP

<pre><code>
config.vm.forwarded_port 80, 8080, protocol: "udp"
<pre><code>


## Host-Only Networking
 * basic usage
 
 > config.vm.network "hostonly", "192.168.33.10"
 
## Bridged Networking

> config.vm.network "bridged"

## Composing Networking Options
* Vagrant allows you to enable multiple network options

> config.vm.forwarded_port 80, 8080
  config.vm.network "hostonly", "192.168.33.10"
  config.vm.network "bridged"
    

## Multimachine Cluster
<section>

 * 快速建立产品网络的多机器环境，例如web服务器、db服务器
 * 建立一个分布式系统，学习他们是如何交互的
 * 测试API和其他组件的通信
 * 容灾模拟，网络断网、机器死机、连接超时等情况
 
</section>


<sesction>
<pre><code>
* defining multiple machine
Vagrant::Config.run do |config| config.vm.box = "precise64"
config.vm.define "web" do |web| web.vm.forward_port 80, 8080
web.vm.provision :shell, path: "provision.sh"
end
config.vm.define "db" do |db| # We'll fill this in soon.
end 
end

</code></pre>
</section>



## Controlling Multiple Machines

* Most commands, such as up, destroy, and reload now take an argument with the name of the machine to affect
* specify multiple targets in the same command
* or use a regular expression


## Communication Between Machines

* define a host-only network on the machines
<pre><code>
Vagrant::Config.run do |config|
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

</code></pre>


## Plugins
<section
* Installation
<pre><code>
$ vagrant plugin install vagrant-example-plugin
$ vagrant plugin install /path/to/foo.gem
$vagrant plugin list

</pre></code>
</section>


<section>
* write a plugin for vagrant
* Developing a Custom Command
* Adding New Configuration Options
* Modifying Existing Vagrant Behavior

</section>

## Provisions
* automatically install software, alter configurations, and more on the machine as part of the vagrant up process

* shell
* chef
* Puppet


## provisioning with shell
<pre><code>
#!/usr/bin/env bash
echo "Installing Apache and setting it up..."
apt-get update >/dev/null 2>&1
apt-get install -y apache2 >/dev/null 2>&1
rm -rf /var/www
ln -fs /vagrant /var/www


config.vm.provision "shell", path: "provision.sh"

</code></pre>


## Working with Docker

* provisioner
* provider
<pre><code>
Vagrant.configure("2") do |config|
  config.vm.provider "docker" do |d|
    d.image = "foo/bar"
  end
end

$ vagrant up --provider=docker
</code></pre>


## Working with Ansible

<pre><code>
config.vm.provision "ansible" do |ansible|
        ansible.playbook = "playbook.yml"
</code></pre>

## FAQ
 
* [Where is Vagrant saving changes to the VM?](http://stackoverflow.com/questions/8225820/where-is-vagrant-saving-changes-to-the-vm/8331810#8331810)
* [Should I use Vagrant or Docker.io for creating an isolated environment?](http://stackoverflow.com/questions/16647069/should-i-use-vagrant-or-docker-io-for-creating-an-isolated-environment/21314566#21314566)
* [Vagrant error : Failed to mount folders in Linux guest](http://stackoverflow.com/questions/22717428/vagrant-error-failed-to-mount-folders-in-linux-guest/23752848#23752848)
* [How to connect to Mysql Server inside VirtualBox Vagrant?](http://stackoverflow.com/questions/10709334/how-to-connect-to-mysql-server-inside-virtualbox-vagrant/10794530#10794530)