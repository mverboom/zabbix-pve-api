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
* Datacenter monitoring of
  * Number of nodes in cluster
* Proxmox node monitoring of
  * CPU
  * Memory
  * Root filesystem
  * ZFS pools
  * Logical Volumes
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

## Architecture

## Configuration

There are two steps in configuring the usage of this template set:

1. Creating an API token in Proxmox
2. Configuring the template
3. Optional configuration

### Creating API token

Access to the information in Proxmox is done through the API and needs authentication. For this
an API token is used.

Withing Proxmox an API token is configured on the datacenter level and is valid for all nodes in a
cluster. So for a set of machines that form a cluster, only 1 API token is needed.

To generate an API token a valid user needs to be present. Depending on security requirements different
options can be used to setup the API token. The documentation within Proxmox can help here.

The setup can be found on the Proxmox server in the following place

* Open the Proxmox management interface
```https://proxmoxserver:8006```
* Go to: Datacenter -> Permissions -> API Tokens

This part of the interface will allow you to create a token. The two parts that need to be saved are:

* TokenID (user@authenticationrealm!tokenid)
* Secret (xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)

### Configuring the template

To enable the template on a Proxmox server, go to the host within Zabbix:
* Open Zabbix web interfaces
* Go to Configuration -> Hosts
* Select the host

Now assign the template
* Go to tab ```Templates```
* In ```Link new templates``` ```select pve api datacenter```

Set mandatory macro's
* Go to tab ```Macros```
* Press ```Add``` and set values:
  * Macro: {$PVETOKENID}
  * Value: The generated Proxmox TokenID
* Press ```Add``` and set values:
  * Macro: {$PVESECRET}
  * Value: The generated Proxmox Secret
* Press ```Add``` and set values:
  * Macro: {$PVESERVER}
  * Value: The hostname to use for connecting to the Proxmox API
* Press Update

### Optional configuration

Optionally there are some other macro's that have a default setting in the
Template but can be overridden.

```{$PVESNAPSHOTAGE}```

Default value: 604800 seconds (1 week)

Any snapshots detected for a Qemu virtual machine or LXC container older than
this value will generate an alert. The value of this macro needs to be set
in seconds.

```{$PVESNAPSHOTEXCLUDE}```

Default value: test

Any snapshot names staring with the value of this macro will be excluded from
generating alerts once the snapshot is older then the value specified in the
{$PVESNAPSHOTAGE} macro.
The value will match the start of the name. So the default value of ```test```
will match:
* test
* test_snapshot
* testing

The value of the macro is used in a regular expressing. Therefore it is possible
to specify multiple snapshot names that will be excluded. For example the value
```(test|upgrade)``` will match all snapshot names starting with
* test
* upgrade

```{$PVETESTPOOL}```

Default value: test

Any Qemu virtual machines or LXC containers assigned to a pool where the name
of the pool start with the value of this macro will be excluded from generating
alerts.
The value will match the start of the name of the pool. So the default value 
of ```test``` will match:
* test
* testpool
* testing

The value of the macro is used in a regular expressing. Therefore it is possible
to specify multiple pool names that will be excluded. For example the value
```(test|project)``` will match all pools names starting with
* test
* project
