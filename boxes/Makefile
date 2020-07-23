all: centos-8-docker

centos-8-docker: centos-8-docker.box

centos-8-docker.box:
	packer build centos-8-docker.json
	mv output-vagrant/package.box centos-8-docker.box
	rm -f output-vagrant/Vagrantfile
	rm -rf output-vagrant/.vagrant
	rmdir output-vagrant

