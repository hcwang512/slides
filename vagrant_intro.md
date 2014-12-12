## An Introduction to Vagrant
\
\
![](/Users/wanghongcheng/Downloads/fig/logo_vagrant.png)
\ 王洪城
\ 2014.12
@huhulab

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
 > config.vm.forwarded_port 80, 8080
 
* Collision Detection and Correction

> config.vm.forwarded_port 80, 8080, auto_correct: true

  config.vm.usable_port_range = (2200..2250)

* TCP or UDP

> config.vm.forwarded_port 80, 8080, protocol: "udp"

## Host-Only Networking
 * basic usage
 
 > config.vm.network "hostonly", "192.168.33.10"
 
## Bridged Networking

> config.vm.network "bridged"

## Composing Networking Options
* Vagrant allows you to enable multiple network options

> config.vm.forwarded_port 80, 8080


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
end

</code></pre>








## Some Useful Command



## Some Plugins
* Installation
<pre>
$ vagrant plugin install vagrant-example-plugin
</pre>


## Provisions
* automatically install software, alter configurations, and more on the machine as part of the vagrant up process


## Work with Docker  dvm

## Work with Ansible

## FAQ
 list some problems in overstack