# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV["LC_ALL"] = "en_US.UTF-8"

cluster = {
  "client1" => {
      "fqdn" => "client1.lab",
      "ipv4" => "192.168.33.100"
  },
  "gluster1" => {
      "fqdn" => "gluster1.lab",
      "ipv4" => "192.168.33.101"
  },
  "gluster2" => {
      "fqdn" => "gluster2.lab",
      "ipv4" => "192.168.33.102"
  },
  "gluster3" => {
      "fqdn" => "gluster3.lab",
      "ipv4" => "192.168.33.103"
  }
}

groups = {
  "linux"   => ["client1", "gluster1", "gluster2", "gluster3"],
  "gluster" => ["gluster1", "gluster2", "gluster3"],
  "client"  => ["client1"],
  "ganesha" => ["client1"]
}

Vagrant.configure(2) do |config|

  config.vm.box = "ubuntu/trusty64"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  # Set up the nodes
  cluster.each_with_index do |(hostname, info), index|
    config.vm.define hostname do |node|
      node.vm.hostname = hostname
      node.vm.network "private_network", ip: "#{info['ipv4']}"

      node.vm.provider "virtualbox" do |vb|
        vb.name = hostname
        vb.memory = "512"

        # Don't display the VirtualBox GUI when booting the machine
        vb.gui = false
        
        # Get disk path
        disk_file = File.join(Dir.pwd, '.vagrant', 'machines', vb.name, 'virtualbox', 'disk2.vdi')

        unless hostname == "client1"
          # Create an extra block device for Gluster nodes.
          unless File.exist?(disk_file)
            vb.customize ['createhd', '--filename', disk_file, '--size', 1 * 1024]
          end
          vb.customize ['storageattach', :id, '--storagectl', 'SATAController', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', disk_file]
        end
      end

      # Wait until all nodes are provisioned before running Ansible.
      if index == cluster.size - 1
        # Configure Gluster using Ansible
        config.vm.provision "ansible" do |ansible|
          ansible.verbose = "v"
          ansible.limit = "all"
          ansible.groups = groups
          ansible.host_vars = cluster
          ansible.playbook = "setup.yml"
        end
      end
    end
  end
end
