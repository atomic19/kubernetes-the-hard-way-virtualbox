# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.box_check_update = false
  config.vm.provider "virtualbox" do |vb|
#    vb.cpus = 1
#    vb.memory = 768
  end

  (0..2).each do |i|
    config.vm.define "controller-#{i}" do |node|
      node.vm.hostname = "controller-#{i}"
      node.vm.network "private_network", ip: "192.168.100.1#{i}"
      node.vm.provision :hosts, :sync_hosts => true
      node.vm.provider "virtualbox" do |vb|
        vb.name = "controller-#{i}"
        vb.cpus = 1
        vb.memory = 1024
      end
      node.vm.provision "shell", path: "heartbeat.sh"
    end
  end

  (0..2).each do |i|
    config.vm.define "worker-#{i}" do |node|
      node.vm.hostname = "worker-#{i}"
      node.vm.network "private_network", ip: "192.168.100.2#{i}"
      node.vm.provision :hosts, :sync_hosts => true
      node.vm.provider "virtualbox" do |vb|
        vb.name = "worker-#{i}"
        vb.cpus = 1
        vb.memory = 512
      end
      node.vm.provision "shell", path: "worker-provision.sh"
      node.trigger.after :up do |t|
        if (i == 2) then
          t.info = 'Running reboot on controller machines'
          t.run = { :inline => 'vagrant reload controller-0 controller-1 controller-2' }
        end
      end
    end
  end
end
