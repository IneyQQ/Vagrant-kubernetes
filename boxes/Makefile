all: centos-8-kubeadm

centos-8-kubeadm: centos-8-kubeadm.box

%.box: %.uncommented.json
	packer build $<
	rm -f $@
	mv output-vagrant/package.box $@
	rm -f output-vagrant/Vagrantfile
	rm -rf output-vagrant/.vagrant
	rmdir output-vagrant

%.uncommented.json: %.json
	jq 'walk(if type == "object" then del(._comment) else . end)' $< > $@
