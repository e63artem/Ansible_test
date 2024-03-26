# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
ENV['VAGRANT_SERVER_URL'] = 'https://vagrant.elab.pro'

Vagrant.configure("2") do |config|  
	config.vm.define "server" do |server|
		server.vm.box = "ubuntu/jammy64"
		server.vm.hostname = "server"
		server.vm.network "private_network", ip: "192.168.50.11"
		server.vm.network "forwarded_port", guest: 80, host: 8080
		server.vm.synced_folder ".","/home/vagrant/"
		server.vm.provision "shell", inline: <<-SHELL
			sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/#g' /etc/ssh/sshd_config
			service ssh restart
			sudo apt update
		SHELL
	end

	config.vm.define "controlnode" do |controlnode|  
		controlnode.vm.box = "ubuntu/jammy64"
		controlnode.vm.hostname = "controlnode"
		controlnode.vm.network "private_network" , ip: "192.168.50.10"
		controlnode.vm.synced_folder "./ansible","/home/vagrant/ansible"
		controlnode.vm.provision "shell", inline: <<-SHELL
			sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/#g' /etc/ssh/sshd_config
			sudo service ssh restart
			sudo apt-add-repository ppa:ansible/ansible
 			sudo apt update && sudo apt install -y ansible
			sudo sh -c "echo '192.168.50.11 ansible_user=vagrant ansible_password=vagrant' >> /etc/ansible/hosts"
			sudo sh -c "echo '[defaults]' >> /etc/ansible/ansible.cfg"
			sudo sh -c "echo 'host_key_checking = False' >> /etc/ansible/ansible.cfg"
			ansible all -m ping
			ansible-playbook /home/vagrant/ansible/playbook.yml
		SHELL
		
	end

end

# config.vm.provision "file", source: File.join(ENV['HOME'], ".ssh", "id_rsa.pub"), destination: "/home/vagrant/.ssh/id_rsa.pub"
#   config.vm.provision "shell" , inline: "cat /home/vagrant/.ssh/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
