# -*- mode: ruby -*-
# vi: set ft=ruby :
#
Vagrant.configure("2") do |config|
  config.vm.define "master" do |config|
    config.vm.provider "virtualbox" do |v|
      v.cpus = 2
      v.memory = 1024
    end
    config.vm.box = "file://boxes/centos-8-kubernetes-master.box"
    config.vm.network "private_network", ip: "192.169.0.2"
    config.vm.hostname = "master.kuber"
    config.landrush.enabled = true
    config.landrush.host "master.kuber", "192.169.0.2"
    config.landrush.upstream "8.8.8.8"
    config.vm.synced_folder ".", "/vagrant", disabled: true
    config.vm.provision "shell", inline: <<~EOF
      sudo kubeadm init
      mkdir -p $HOME/.kube
      sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
      sudo chown $(id -u):$(id -g) $HOME/.kube/config
    EOF
  end
  config.vm.define "slave-0" do |config|
    config.vm.box = "file://boxes/centos-8-kubeadm.box"
    config.vm.network "private_network", ip: "192.169.0.3"
    config.vm.hostname = "slave-0.kuber"
    config.landrush.enabled = true
    config.landrush.host "slave-0.kuber", "192.169.0.3"
    config.landrush.upstream "8.8.8.8"
    config.vm.synced_folder ".", "/vagrant", disabled: true
  end
end
