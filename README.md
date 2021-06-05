# Zabbix PVE API

This template set uses the Proxmox Virtual Environment API to monitor sytems.

This template set was developed on Zabbix 5.4 in combination with Proxmox 6.
It might work on older combinations, but this was not tested.

The template set consists of 4 templates, of which only 1 should be manually
assigned:

* Template pve api settings
* Template pve api datacenter
  * Template pve api node
  * Template pve api lxc
  * Template pve api qemu

The Template pve api datacenter is used for adding a Proxmox single or cluster
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

The idea behind this template set is to monitor a Proxmox "datacenter" through
the Proxmox API. This should allow for a simple way to roll out monitoring of
systems. The disadvantage is that only data that is available through the Proxmox
API will be available for monitoring.

### Authentication

The normal Proxmox API login process requires multiple steps to authenticate and
retrieve a cookie. This isn't something that can be done with Zabbix. However,
Proxmox also has an API Token interface, which is usable through Zabbix. There is
one downside, this API Token needs to be submitted through an HTTP header. The
simplest way to do an HTTP request is through the Zabbix Agent. This method does
not allow for sending headers. The alternative is the HTTP Request method.

### Connecting to API

The HTTP Request method does not require a Zabbix Agent to be active on the system.
It is possible to define a host within Zabbix that has an interface (for example
a zabbix agent to 127.0.0.1) but doesn't use it and has the Template pve api
datacenter applied.

In order to decouple the monitoring further from the host configuration, the
name of the host within Zabbix is not used by the templates to connect to the
API. The hostname is determined by the {$PVESERVER} macro. This is intentional.
This way it is possible for a cluster to define a "dummy" host within zabbix with
the name of the Proxmox cluster. If the node through which the whole cluster is
monitored becomes unavailable or dies, only the {$PVESERVER} macro needs to be
changed to the name of another node in the cluster, and monitoring should
continue.

### Naming conventions

There are some generic terms that are being used:

* scope: {datacenter|node|qemu|lxc}
* type: {master|discovery|<not present>}

#### Template naming

Template names have the following naming scheme:

* Template pve api {scope}

#### Template configuration

The relation between the scopes is as follows:
             +--> node
             |
datacenter --+--> qemu
             |
             +--> lxc

The datacenter template uses host prototypes to create the node, qemu and lxc
instances throught their relative templates.

As there are no items in common between the templates, there is no link between
the node,qemu,lxc templates and the datacenter template. Therefore any macro's
defined in the datacenter template are not available in the node, qemu and lxc
templates. To work around this, there is a:

* Template pve api settings

This only contains macro's to configure the workings of the other templates. All
other templates link to this settings template and inherit the macro's.

#### Item naming

naming scheme items:

* pveapi.<scope>.<type>.name

where name is the item begin monitored, for example cpu.

### Template communcation

The template set consists of 4 templates:

* Template pve api datacenter
  * Template pve api node
  * Template pve api lxc
  * Template pve api qemu

The pve api datacenter template is the main entry point. This template discovers
the cluster and uses Host Prototypes to create:
* nodes
* lxc containers
* qemu virtual machines

Each of the created hosts have their own items and discovery rules to create and
monitor the specific items for the host.


### VM migrations

Because the VM's are not hierarchically linked to the node they run on, data will



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
