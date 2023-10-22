# Zabbix PVE API

This template set uses the Proxmox Virtual Environment API to monitor sytems.

It is in beta state and might work or it might not. Still working on documentation
and testing.

This template set was developed on Zabbix 6.4 in combination with Proxmox 8.
It might work on older combinations, but this was not tested.

There are two templates included in this repository:

* For Zabbix LTS (currently 6.0)
* Development, for version of Zabbix I am currently using (now 6.4)

The template set consists of 4 templates, of which only 1 should be manually
assigned:

* Template pve api settings
* Template pve api datacenter
  * Template pve api node
  * Template pve api lxc
  * Template pve api qemu

The Template pve api datacenter is used for adding a Proxmox single or cluster
installation to Zabbix.

For more information about the architecture of the templates, see [ARCHITECTURE.md](ARCHITECTURE.md).

For some notes on the templates, see [NOTES.md](NOTES.md).

## Features

This is an excerpt of the features of the template set:
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
  * Services
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

## Configuration

There are two steps in configuring the usage of this template set:

1. Creating an API token in Proxmox
2. Configuring the template
3. Optional configuration

A word of warning. There are quite a lot of items that will be monitored when this
template is applied to one or more Proxmox clusters or stand alone systems. There
might be items that are less applicable in certain situations. It can save load on
the Zabbix server to look through the templates and disable specific monitoring of
items if they are not required.

### Creating API token

Access to the information in Proxmox is done through the API and needs
authentication. For this an API token is used.

Withing Proxmox an API token is configured on the datacenter level and is valid
for all nodes in a cluster. So for a set of machines that form a cluster, only
1 API token is needed.

To generate an API token a valid user needs to be present. Depending on
security requirements different options can be used to setup the API token. The
documentation within Proxmox can help here.

The setup can be found on the Proxmox server in the following place

* Open the Proxmox management interface
```https://proxmoxserver:8006```
* Go to: Datacenter -> Permissions -> API Tokens

This part of the interface will allow you to create a token. The two parts that need to be saved are:

* TokenID (user@authenticationrealm!tokenid)
* Secret (xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)

To assign least privileges to the token read the [API-PERMISSIONS.md](API-PERMISSIONS.md).

### Configuring the template

To enable the template on a Proxmox server, go to the host within Zabbix:
* Open Zabbix web interfaces
* Go to Configuration -> Hosts
* Select the host

Make sure the host has an interface. This can be a zabbix agent, and does not have to
communicate with a zabbix-agent on the Proxmox system. It can be the zabbix server, so
for example a zabbix-agent at 127.0.0.1.

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

Default value: ^test$

Any snapshot matching this regular expression will be excluded from triggers
alerting on the age of the snapshot as defined by the {$PVESNAPSHOTAGE} macro.

By using regular expressions it is possible to specify multiple snapshot names
that will be excluded. For example the value
```^(test|upgrade)``` will match all snapshot names starting with
* test
* upgrade

```{$PVENOPROBLEMPOOL}```

Default value: ^test$

Any Qemu virtual machines or LXC containers assigned to a pool where the name
of the pool matches the regex of this macro will be excluded from generating
problems.
The value will match the name of the pool. So the default value
of ```^test$``` will match exactly:
* test

The value of the macro is used in a regular expression. Therefore it is possible
to specify multiple pool names that will be excluded. For example the value
```(test|project)``` will match all pool names containing 'test' and 'project':
* test
* project
* testmachines
* exampleproject

And a value of ```^(development|test|acceptance)$``` will match all pool pool names that have a name of exactly:
* development
* test
* acceptance

The value ```^(((?!^production$).)*)$``` is a negative lookahead regex which will match all pools, except for pool 'production'

Testing regexes can be done here: https://regex101.com
