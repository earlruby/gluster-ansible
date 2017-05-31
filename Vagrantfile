# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV["LC_ALL"] = "en_US.UTF-8"

# Valid values are:
#  * ubuntu/trusty64
#  * ubuntu/xenial64
vm_os = "ubuntu/xenial64"

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

# Valid gluster_repo values are "glusterfs-3.8", "glusterfs-3.9", and "glusterfs-3.10"
groups = {
  "linux"   => ["client1", "gluster1", "gluster2", "gluster3"],
  "gluster" => ["gluster1", "gluster2", "gluster3"],
  "client"  => ["client1"],
  "linux:vars" => {
    "gluster_repo" => "glusterfs-3.10"
  }
}

Vagrant.configure(2) do |config|

  config.vm.box = vm_os
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
        disk_file = File.join(Dir.pwd, '.vagrant', 'machines', vb.name, 'virtualbox', 'brick1.vmdk')

        unless hostname == "client1"
          # Create an extra block device for Gluster nodes.
          unless File.exist?(disk_file)
            vb.customize ['createmedium', 'disk', '--format', 'vmdk', '--filename', disk_file, '--size', 1 * 1024, '--variant', 'standard']
          end
          # Use 'VBoxManage showvminfo $vmname' to find out what storage controllers are supported
          if vm_os == "ubuntu/trusty64"
            vb.customize ['storageattach', :id, '--storagectl', 'SATAController', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', disk_file]
          else
            vb.customize ['storageattach', :id, '--storagectl', 'SCSI', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', disk_file]
          end
        end
      end

      # Wait until all nodes are provisioned before running Ansible.q
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
