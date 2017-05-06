# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV["LC_ALL"] = "en_US.UTF-8"

cluster = {
  "gfs-1" => {
      "fqdn" => "gfs-1.lab",
      "ipv4" => "192.168.33.101"
  },
  "gfs-2" => {
      "fqdn" => "gfs-2.lab",
      "ipv4" => "192.168.33.102"
  },
  "gfs-3" => {
      "fqdn" => "gfs-3.lab",
      "ipv4" => "192.168.33.103"
  }
}

groups = {
  "linux" => ["gfs-1", "gfs-2", "gfs-3"],
  "gfs"   => ["gfs-1", "gfs-2", "gfs-3"]
}

Vagrant.configure(2) do |config|

  config.vm.box = "ubuntu/trusty64"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  # Set up 3 VMs
  cluster.each_with_index do |(hostname, info), index|
    config.vm.define hostname do |node|
      node.vm.hostname = hostname
      node.vm.network "private_network", ip: "#{info['ipv4']}"


      node.vm.provider "virtualbox" do |vb|
        vb.name = hostname

        # Display the VirtualBox GUI when booting the machine
        vb.gui = true

        # Customize the amount of memory on the VM:
        vb.memory = "1024"

        # Get disk path
        disk_file = File.join(Dir.pwd, '.vagrant', 'machines', vb.name, 'virtualbox', 'disk2.vdi')

        # Create an extra block device for Gluster
        unless File.exist?(disk_file)
          vb.customize ['createhd', '--filename', disk_file, '--size', 1 * 1024]
        end
        vb.customize ['storageattach', :id, '--storagectl', 'SATAController', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', disk_file]
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
