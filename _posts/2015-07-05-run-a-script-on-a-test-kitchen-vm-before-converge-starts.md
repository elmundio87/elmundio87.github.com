---
layout: post
title: "Run a script on a Test Kitchen VM before converge starts"
description: ""
category: Configuration Management
tags: [test-kitchen,vagrant,ansible,testing]
  - /configuration management/2015/07/05/run-a-script-on-a-test-kitchen-vm-before-converge-starts/
---
Recently I hit a snag while using a community vagrant box to test an Ansible role using test-kitchen. 

At the point at which test-kitchen would attempt to install ansible, it would complain that the package "ansible"
was not a valid package. Apparently this is due to the fact that you need to install the ```epel-release``` package
prior to installing ansible.

Annoyingly, the VM did not already have ```epel-release``` installed, and I needed a workaround for this.

In the [kitchen-vagrant documentation](https://github.com/test-kitchen/kitchen-vagrant/blob/master/README.md) there is mention 
of a ```vagrantfiles``` property. This allows you to merge custom vagrant configuration into the vagrantfile that test-kitchen
generates behind the scenes.

Create a file called *vagrant.rb* and place it in the same directory as *.kitchen.yml* .

#### vagrant.rb
``` ruby
Vagrant.configure(2) do |config|
  config.vm.provision "shell", inline: <<-SHELL
     sudo yum install -y epel-release
     sudo yum install -y ansible
  SHELL
end
```

#### .kitchen.yml
``` ruby
platforms:
  - name: centos-7.0
    driver:
      box: centos7-64
      provision: true
      vagrantfiles:
        - vagrant.rb
```

This functionality has a wide range of uses, which could include adding new NICS or disks independently of the base box.