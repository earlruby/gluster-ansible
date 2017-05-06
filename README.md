# gluster-ansible

Ansible playbooks for GlusterFS

## Description

For people wanting to test Gluster, you can use these tools to create a 3-node Gluster cluster
using Vagrant and then provision the cluster using Ansible.

## Prerequisites

You must install the following tools on your host:

* Vagrant
* Virtualbox
* Ansible

You'll also need about 3GB RAM free on your host.

### Tested with

* Vagrant 1.9.4
* Virtualbox 5.1.20
* Ansible 2.3.0.0
* Ubuntu Trusty 14.04 64-bit
* Gluster 3.8.11
* libntirpc 1.4.3

## Building a cluster

Check out this repo, then:

```
cd gluster-ansible
vagrant up --provider virtualbox
```

This will set up three VMs, gfs-1 (192.168.33.101), gfs-2 (192.168.33.102), and gfs-3 (192.168.33.103)
running on a private network visible only to your host computer.

Vagrant will generate an Ansible inventory file in `.vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory`.

## Changing Gluster settings in Ansible

Once your Gluster cluster is up, if you want to make changes to the Ansible configuration and apply those changes
to your cluster, simply type:

```
vagrant provision
```
