# -*- mode: ruby -*-
# vi: set ft=ruby :

hosts = {
  'master' => {
    'count' => 3,
    'ip_prefix' => '192.168.33.1',
    'node_ip_prefix' => '10.0.33.1'
  },

  'node' => {
    'count' => 1,
    'ip_prefix' => '192.168.33.2',
    'node_ip_prefix' => '10.0.33.2'
  }
}

inventory = File.open('vagrant_inventory', 'w')

# host section
hosts.each_pair do |type, data|
  (1..data['count']).each do |i|
    inventory.puts("#{type}#{i} ansible_host=#{data['ip_prefix']}#{i} node_ip=#{data['node_ip_prefix']}#{i} ansible_ssh_private_key_file=.vagrant/machines/#{type}#{i}/virtualbox/private_key")
  end
end

# group section
hosts.each_pair do |type, data|
  inventory.puts("[#{type}]")
  (1..data['count']).each do |i|
    inventory.puts("#{type}#{i}")
  end
end

inventory.close

Vagrant.configure("2") do |config|

  if ARGV[1] == 'base'
    
    config.vm.box = "debian/buster64"
    config.vm.define "base" do |machine|
      machine.vm.hostname = "base"
      machine.vm.provider "virtualbox" do |vb|
        vb.cpus = 2
        vb.memory = 2048
      end
    end
    config.vm.post_up_message = "vagrant package base --output k8s.box && vagrant box add k8s-base k8s.box -f && vagrant destroy base -f"

  else
    config.vm.box = "k8s-base"

    hosts.each_pair do |type, data|
      (1..data['count']).each do |i|
        
        config.vm.define "#{type}#{i}" do |machine|
          machine.vm.network "private_network", ip: "#{data['ip_prefix']}#{i}"
          machine.vm.network "private_network", ip: "#{data['node_ip_prefix']}#{i}"
          machine.vm.hostname = "#{type}#{i}"
          machine.vm.provider "virtualbox" do |vb|
              vb.cpus = 2
              vb.memory = 2048
          end
        end
      end
    end
  end

  config.vm.provision "ansible" do |ansible|
    ansible.verbose = "vv"
    ansible.playbook = "k8s/playbook.yml"

    # Use tag "base" based on ARGV
    if ARGV[1] == 'base'
      ansible.tags = "base"

      # Needed for installing everything
      ansible.groups = {
        "master" => ["base"]
      }
    else
      ansible.inventory_path = "vagrant_inventory"
      ansible.skip_tags = "base"
    end

  end

end
