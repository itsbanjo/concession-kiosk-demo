# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
    v.cpus = 2
  end

  # Will not check for box updates during every startup.
  config.vm.box_check_update = false


  # frontend
  config.vm.define "frontend" do |frontend|
    ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    frontend.vm.provision 'shell', inline: 'mkdir -p /root/.ssh'
    frontend.vm.provision 'shell', inline: "echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys", privileged: false
    frontend.vm.provision 'shell', inline: "echo #{ssh_pub_key} >> /root/.ssh/authorized_keys"
    frontend.vm.box = "generic/rhel8"
    frontend.vm.hostname = "frontend.mylab.com"
    frontend.vm.network "public_network", ip: "192.168.100.103"
  end


  # backend
  config.vm.define "backend" do |backend|
    ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    backend.vm.provision 'shell', inline: 'mkdir -p /root/.ssh'
    backend.vm.provision 'shell', inline: "echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys", privileged: false
    backend.vm.provision 'shell', inline: "echo #{ssh_pub_key} >> /root/.ssh/authorized_keys"
    backend.vm.box = "generic/rhel8"
    backend.vm.hostname = "backend.mylab.com"
    backend.vm.network "public_network", ip: "192.168.100.104"
  end

  # mongodb
  config.vm.define "mongodb" do |mongodb|
    ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    mongodb.vm.provision 'shell', inline: 'mkdir -p /root/.ssh'
    mongodb.vm.provision 'shell', inline: "echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys", privileged: false
    mongodb.vm.provision 'shell', inline: "echo #{ssh_pub_key} >> /root/.ssh/authorized_keys"
    mongodb.vm.box = "generic/rhel8"
    mongodb.vm.hostname = "mongodb.mylab.com"
    mongodb.vm.network "public_network", ip: "192.168.100.105"
  end

end
