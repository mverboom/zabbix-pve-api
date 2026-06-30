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

### Macro inheritance to discovered hosts

Hosts created by host prototypes do not inherit host groups from the parent
(datacenter) host. However, Zabbix does inherit host-level macros from the
parent host to discovered hosts through its "master host macro" mechanism.

This means any macro defined at the host level on the datacenter host (not on
a template) is automatically copied to all discovered hosts (nodes, qemu, lxc)
with its actual value. This mechanism is already used for the API connection
macros (`{$PVESERVER}`, `{$PVETOKENID}`, `{$PVESECRET}`), which the discovered
hosts need for their own API calls.

The settings template macros (like `{$PVESNAPSHOTAGE}`) are available on
discovered hosts through normal template inheritance (discovered host ->
node/qemu/lxc template -> settings template). If a macro is defined both on the
settings template and at the host level on the datacenter host, the host-level
value takes precedence on discovered hosts.

### Alert routing

All triggers in this template set carry a tag named `alert_group` with the
value `{$PVEALERTGROUP}`. This macro is defined on the settings template with
an empty default. By setting it at the host level on a datacenter host, the
value is inherited by all discovered hosts in that cluster. Trigger actions can
then use the `alert_group` tag to route notifications to specific teams.

See the README.md for configuration instructions.

### VM migrations

Because the VM's are not hierarchically linked to the node they run on, data will
