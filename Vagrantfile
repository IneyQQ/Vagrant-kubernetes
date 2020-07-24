# -*- mode: ruby -*-
# vi: set ft=ruby :
#

master_ip = "192.169.0.2"
Vagrant.configure("2") do |config|
  config.vm.define "master" do |config|
    config.vm.provider "virtualbox" do |v|
      v.cpus = 2
      v.memory = 1024
    end
    config.vm.box = "centos-8-kubeadm"
    config.vm.network "private_network", ip: master_ip
    config.vm.hostname = "master.kuber"
    config.landrush.enabled = true
    config.landrush.host "master.kuber", master_ip
    config.landrush.upstream "8.8.8.8"
    config.vm.synced_folder ".", "/vagrant", disabled: true
    config.vm.provision "shell", inline: <<~EOF
      sudo firewall-cmd --permanent --add-port=6443/tcp
      sudo firewall-cmd --permanent --add-port=2379-2380/tcp
      sudo firewall-cmd --permanent --add-port=10250/tcp
      sudo firewall-cmd --permanent --add-port=10251/tcp
      sudo firewall-cmd --permanent --add-port=10252/tcp
      sudo firewall-cmd --permanent --add-port=10255/tcp
      sudo firewall-cmd --reload
      sudo modprobe br_netfilter
      echo '1' | tee /proc/sys/net/bridge/bridge-nf-call-iptables > /dev/null
      sudo swapoff -a
      sudo sed -i '/swap/ s/^#*/#/' /etc/fstab
    EOF
    config.vm.provision "shell", inline: <<~EOF
      sudo kubeadm init --apiserver-advertise-address #{master_ip}
      mkdir -p $HOME/.kube
      sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
      sudo chown $(id -u):$(id -g) $HOME/.kube/config
    EOF
  end
  config.vm.define "slave-0" do |config|
    config.vm.box = "centos-8-kubeadm"
    config.vm.network "private_network", ip: "192.169.0.3"
    config.vm.hostname = "slave-0.kuber"
    config.landrush.enabled = true
    config.landrush.host "slave-0.kuber", "192.169.0.3"
    config.landrush.upstream "8.8.8.8"
    config.vm.synced_folder ".", "/vagrant", disabled: true
    config.vm.provision "shell", inline: <<~EOF
      firewall-cmd --permanent --add-port=6783/tcp
      firewall-cmd --permanent --add-port=10250/tcp
      firewall-cmd --permanent --add-port=10255/tcp
      firewall-cmd --permanent --add-port=30000-32767/tcp
      firewall-cmd  --reload
      echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
      sudo swapoff -a
      sudo sed -i '/swap/ s/^#*/#/' /etc/fstab
    EOF
    config.vm.provision "shell", path: "join_cluster.sh"
  end
end
