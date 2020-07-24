all: centos-8-kubernetes-master

centos-8-kubernetes-master: centos-8-kubeadm centos-8-kubernetes-master.box

centos-8-kubeadm: centos-8-docker centos-8-kubeadm.box

centos-8-docker: centos-8-docker.box

%.box: %.json
	packer build $<
	rm -f $@
	mv output-vagrant/package.box $@
	rm -f output-vagrant/Vagrantfile
	rm -rf output-vagrant/.vagrant
	rmdir output-vagrant
