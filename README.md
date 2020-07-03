# Zabbix PVE API

This template set uses the Proxmox Virtual Environment API to monitor sytems.

The template set consists of 4 templates, of which only 1 should be manually
assigned:

* pve api datacenter
  * pve api node
  * pve api lxc
  * pve api qemu

The pve api datacenter template is used for adding a Proxmox single or cluster
installation to Zabbix.

## Features

This is an exceprt of the features of the template set:
* Automatic creation of host objects for:
  * All Proxmox nodes in a cluster (or single host for non clusters)
  * LXC containers
  * Qemu virtual machines
* Proxmox node monitoring of
  * CPU
  * Memory
  * Root filesystem
  * ZFS pools
  * Disk smart status
* LXC container monitoring of
  * CPU
  * Memory
  * Global network usage
  * Diskspace
  * Snapshots
* Qemu virtual machine monitoring of
  * CPU
  * Memory
  * Network usage per interface
  * Disk controllers
  * Snapshots
