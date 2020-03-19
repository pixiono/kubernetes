# -*- mode: ruby -*-
# vi: set ft=ruby :

MASTERS = 3
NODES = 1

Vagrant.configure("2") do |config|
  config.vm.box = "debian/stretch64"

  (1..MASTERS).each do |i|
    config.vm.define "master#{i}" do |master|
      master.vm.network "private_network", ip: "192.168.33.1#{i}"
      master.vm.hostname = "master#{i}"
      master.vm.provider "virtualbox" do |vb|
          vb.cpus = 2
          vb.memory = 2048
      end
    
      master.vm.provision "ansible" do |ansible|
        ansible.verbose = "v"
        ansible.playbook = "k8s/playbook.yml"
        ansible.extra_vars = {
          ansible_python_interpreter: "/usr/bin/python3",
          node_ip: "192.168.33.1#{i}",
          k8s_type: "master"
        }
      end
    end
  end


  (1..NODES).each do |i|
    config.vm.define "node#{i}" do |node|
      node.vm.network "private_network", ip: "192.168.33.2#{i}"
      node.vm.hostname = "node#{i}"
      node.vm.provider "virtualbox" do |vb|
          vb.cpus = 2
          vb.memory = 2048
      end
    
      node.vm.provision "ansible" do |ansible|
        ansible.verbose = "v"
        ansible.playbook = "k8s/playbook.yml"
        ansible.extra_vars = {
          ansible_python_interpreter: "/usr/bin/python3",
          node_ip: "192.168.33.21",
          k8s_type: "worker"
        }
      end
    end
  end

  config.vm.define "proxy" do |proxy|
    proxy.vm.network "private_network", ip: "192.168.33.2"

    proxy.vm.network :forwarded_port, guest: 22, host: 1234
    proxy.vm.hostname = "proxy"
    proxy.vm.provider "virtualbox" do |vb|
        vb.cpus = 1
        vb.memory = 1024
    end
  end
end
