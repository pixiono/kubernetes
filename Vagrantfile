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
  config.vm.box = "debian/buster64"

  hosts.each_pair do |type, data|
    (1..data['count']).each do |i|
      
      config.vm.define "#{type}#{i}" do |machine|
        machine.vm.network "private_network", ip: "#{data['ip_prefix']}#{i}"
        machine.vm.network "private_network", ip: "#{data['node_ip_prefix']}#{i}"
        machine.vm.hostname = "master#{i}"
        machine.vm.provider "virtualbox" do |vb|
            vb.cpus = 2
            vb.memory = 2048
        end
      end
    end
  end

  config.vm.provision "ansible" do |ansible|
    # ansible.verbose = "vvvv"
    ansible.playbook = "k8s/playbook.yml"
    ansible.inventory_path = "vagrant_inventory"
  end

end
