# Ansible playbooks for GlusterFS

## Description

For people wanting to test Gluster, you can use these tools to create a 3-node Gluster cluster
plus an NFS client node running Ganesha using Vagrant. Vagrant provisions the nodes using Ansible.

 * Learn how to administer Gluster and Ganesha.
 * Test different Gluster settings and configurations.
 * Teach yourself how to add hosts, remove hosts, and replace hosts on a live cluster.
 * Test various failure modes, then recover from those failures.
 * Try different versions of Gluster.

## Prerequisites

You must install the following tools on your host:

* Vagrant
* Virtualbox
* Ansible

You'll also need about 2GB RAM free on your host.

### Tested with

### Tools

* Vagrant 1.9.4, 1.9.5 (Vagrantfile does not work on 1.9.1)
* Virtualbox 5.1.20
* Ansible 2.2.1.0, 2.3.0.0

### Operating Systems

* Ubuntu Trusty 14.04 64-bit
* Ubuntu Xenial 16.04 64-bit

### Packages

* Gluster 3.8.11 (on Ubuntu Trusty)
* Gluster 3.8.12 (on Ubuntu Trusty)
* Gluster 3.10.2 (on Ubuntu Xenial)
* libntirpc 1.4.3
* Ganesha 2.4.5

## Building a cluster

Check out this repo, then:

```
cd gluster-ansible
vagrant up --provider virtualbox
```

This will set up four VMs: `client1` (192.168.33.100), `gluster1` (192.168.33.101), `gluster2` (192.168.33.102), and `gluster3` (192.168.33.103) running on a private network visible only to your host computer.

Vagrant will generate an Ansible inventory file in `.vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory`.

`client1` mounts the Gluster volume twice:

* `/mnt/gluster` mounts `volume1` using the Gluster protocol.
* `/mnt/ganesha` mounts `volume1` using the NFSv4.1 protocol using the Ganesha NFS server running on `client1`. The Gluster cluster provides backing storage for the Ganesha NFS server.

## Changing Gluster settings in Ansible

Once your Gluster cluster is up, if you want to make changes to the Ansible configuration and apply those changes to your cluster, simply type:

```
vagrant provision
```
