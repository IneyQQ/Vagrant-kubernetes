# -*- mode: ruby -*-
# vi: set ft=ruby :
#

master_ip = "192.169.0.2"
master_dns = "master.kuber"
cluster_cidr = "10.96.0.0/16"
Vagrant.configure("2") do |config|
  config.vm.define "master" do |config|
    config.vm.provider "virtualbox" do |v|
      v.cpus = 2
      v.memory = 2048
    end
    config.vm.box = "centos-8-kubeadm"
    config.vm.network "private_network", ip: master_ip, bridge: "eth1"
    config.vm.hostname = master_dns
    config.vm.base_mac = nil
    config.vm.synced_folder ".", "/vagrant", disabled: true
    config.vm.provision "shell", inline: <<~EOF
      sudo iptables -A INPUT -p tcp --dport 6443 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
      sudo iptables -A INPUT -p tcp --dport 2379 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
      sudo iptables -A INPUT -p tcp --dport 2380 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
      sudo iptables -A INPUT -p tcp --dport 10250 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
      sudo iptables -A INPUT -p tcp --dport 10251 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
      sudo iptables -A INPUT -p tcp --dport 10252 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
      sudo modprobe br_netfilter
      echo '1' | tee /proc/sys/net/bridge/bridge-nf-call-iptables > /dev/null
      sudo swapoff -a
      sudo sed -i '/swap/ s/^#*/#/' /etc/fstab
      echo #{master_ip} #{master_dns} | tee -a /etc/hosts
    EOF
    config.vm.provision "shell", inline: <<~EOF
      sudo kubeadm init --apiserver-advertise-address #{master_ip} --service-dns-domain #{master_dns} --pod-network-cidr=#{cluster_cidr}
      mkdir -p $HOME/.kube
      sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
      sudo chown $(id -u):$(id -g) $HOME/.kube/config
      kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
    EOF
  end
  config.vm.define "slave-0" do |config|
    config.vm.provider "virtualbox" do |v|
      v.cpus = 1
      v.memory = 1024
    end
    config.vm.box = "centos-8-kubeadm"
    config.vm.network "private_network", ip: "192.169.0.3"
    config.vm.hostname = "slave-0.kuber"
    config.vm.base_mac = nil
    config.vm.synced_folder ".", "/vagrant", disabled: true
    config.vm.provision "shell", inline: <<~EOF
      sudo iptables -A INPUT -p tcp --dport 10250 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
      sudo iptables -A INPUT -p tcp -m multiport --dports 30000:32767 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
      echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
      sudo swapoff -a
      sudo sed -i '/swap/ s/^#*/#/' /etc/fstab
      echo 192.169.0.3 slave-0.kuber | tee -a /etc/hosts
    EOF
    config.vm.provision "join_cluster", type: "shell", path: "join_cluster.sh"
  end
  config.vm.define "slave-1" do |config|
    config.vm.provider "virtualbox" do |v|
      v.cpus = 1
      v.memory = 1024
    end
    config.vm.box = "centos-8-kubeadm"
    config.vm.network "private_network", ip: "192.169.0.4"
    config.vm.hostname = "slave-1.kuber"
    config.vm.base_mac = nil
    config.vm.synced_folder ".", "/vagrant", disabled: true
    config.vm.provision "shell", inline: <<~EOF
      sudo iptables -A INPUT -p tcp --dport 10250 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
      sudo iptables -A INPUT -p tcp -m multiport --dports 30000:32767 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
      echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
      sudo swapoff -a
      sudo sed -i '/swap/ s/^#*/#/' /etc/fstab
      echo 192.169.0.4 slave-1.kuber | tee -a /etc/hosts
    EOF
    config.vm.provision "join_cluster", type: "shell", path: "join_cluster.sh"
  end
end
