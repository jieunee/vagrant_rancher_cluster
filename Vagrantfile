# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.a

VAGRANTFILE_API_VERSION = "2"
NODE_COUNT = 3
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
	config.vm.provision "shell", path: "./provisioning/default_setting.sh", args: ""
	config.vm.synced_folder "~/.ssh/", "/tmp/conf.d/"

	## For Rancher VM Hosts instance  
	NODE_COUNT.times do |i|
		node_id = "vmhost0#{i}"
		config.vm.define node_id do |node|
			### Args= [service name] [master / slave] [master IP] [advertise IP]
			node.vm.provision "shell", path: "./provisioning/vmhosts.sh", args: ""
			node.vm.network "forwarded_port", guest: 80, host: "880#{i}"
			#    config.vm.network "private_network", ip: "192.158.10.1#{i}"
			#    config.vm.network "public_network", :dev => "br0", :mode => "bridge", :type => "bridge", :ip => "192.158.10.1#{i}", :netmask => "255.255.255.0", :auto_config => "false"
			node.vm.network "private_network", :dev => "eth0", :ip => "192.200.10.1#{i}"
			node.vm.box = "ubuntu/xenial64"
			node.vm.hostname = "#{node_id}"
			node.vm.provider :virtualbox do |vb|
				vb.customize ["modifyvm", :id, "--memory", "3072"]
				vb.customize ["modifyvm", :id, "--cpus", "2"]
			end
		end
	end

	## For Management server for all same service farm support
	config.vm.define "rancher", primary: true do |rancher|
		rancher.vm.provision "shell", path: "./provisioning/rancher.sh", args: ""
		#    config.vm.network "public_network", :dev => "br0", :mode => "bridge", :type => "bridge0", :ip => "192.158.10.100", :netmask => "255.255.255.0", :auto_config => "false"
		rancher.vm.network "private_network", :dev => "eth0", :ip => "192.200.10.100"
		rancher.vm.network "forwarded_port", guest: 8080, host: 8080
		rancher.vm.box = "ubuntu/xenial64"

		rancher.vm.provider :virtualbox do |vb|
			vb.customize ["modifyvm", :id, "--memory", "2048"]
			vb.customize ["modifyvm", :id, "--cpus", "2"]   
		end 
	end
end
