## Required privileges

See below for more detail on what is required for each of the accesspoints
in the PVE API.

The minimum permission roles seem to be:
* VM.Audit
* Pool.Audit
* Sys.Audit

The built in role PVEAuditor seems to be the closest to this:
* Datastore.Audit
* Pool.Audit
* SDN.Audit
* Sys.Audit
* VM.Audit

But it is also possible to define a new role with specific privileges.

## Configuring permissions

In order to create this:
* Log in to the proxmox web interfaces
* Go to: Datacenter -> Permissions -> Roles
* Go to Permissions
* Add -> API Token Permission
  * Path: /
  * API Token: zabbix@pve!zabbix
  * Role: PVEZabbixMonitor
* Click Add
* Record the informatio for configuration in the template.

## Permission inventory

There are multiple accesspoints to the API that are being used to retrieve
information.

In order to determine the minimum privileges that are required
in order for the template set to work, below are the API access points used per
Template and the privilegs according to the Promox API Viewer:

https://pve.proxmox.com/pve-docs/api-viewer/

Template pve api datacenter
* pveapi datacenter master resources
  * https://{$PVESERVER}:8006/api2/json/cluster/resources
  * Accessible by all authenticated users
* pveapi datacenter master tasks
  *  https://{$PVESERVER}:8006/api2/json/cluster/tasks
  * Accessible by all authenticated users.
* Summary: authenticated user

Template pve api lxc
*  Pool check
  * https://{$PVESERVER}:8006/api2/json/pools
  * List all pools where you have Pool.Audit permissions on /pool/<pool>.
* pveapi lxc master snapshot
  * https://{$PVESERVER}:8006/api2/json/nodes/{$PVENODE}/lxc/{$PVELXCID}/snapshot
  * Check: ["perm","/vms/{vmid}",["VM.Audit"]]
* pveapi lxc master status
  * https://{$PVESERVER}:8006/api2/json/nodes/{$PVENODE}/lxc/{$PVELXCID}/status/current
  * Check: ["perm","/vms/{vmid}",["VM.Audit"]]
* Summary: VM.Audit + Pool.Audit

Template pve api node
* pveapi node master disks
  * https://{$PVESERVER}:8006/api2/json/nodes/{$PVENODE}/disks/list
  * Check: ["or",["perm","/",["Sys.Audit","Datastore.Audit"],"any",1],["perm","/nodes/{node}",["Sys.Audit","Datastore.Audit"],"any",1]]
* pveapi node master lvm
  * https://{$PVESERVER}:8006/api2/json/nodes/{$PVENODE}/disks/lvm
  * Check: ["perm","/",["Sys.Audit","Datastore.Audit"],"any",1]
* pveapi node master lvmthin
  * https://{$PVESERVER}:8006/api2/json/nodes/{$PVENODE}/disks/lvmthin
  * Check: ["perm","/",["Sys.Audit","Datastore.Audit"],"any",1]
* pveapi node master services
  * https://{$PVESERVER}:8006/api2/json/nodes/{$PVENODE}/services
  * Check: ["perm","/nodes/{node}",["Sys.Audit"]]
* pveapi node master status
  * https://{$PVESERVER}:8006/api2/json/nodes/{$PVENODE}/status
  * Check: ["perm","/nodes/{node}",["Sys.Audit"]]
* pveapi node master storagepools
  * https://{$PVESERVER}:8006/api2/json/nodes/{$PVENODE}/storage
  * Only list entries where you have 'Datastore.Audit' or 'Datastore.AllocateSpace' permissions on '/storage/<storage>'
* pveapi node master zfs
  * https://{$PVESERVER}:8006/api2/json/nodes/{$PVENODE}/disks/zfs
  * Check: ["perm","/",["Sys.Audit","Datastore.Audit"],"any",1]
* Summary: Sys.Audit + Datastore.Audit

Template pve api qemu
* Pool check
  * https://{$PVESERVER}:8006/api2/json/pools
  * List all pools where you have Pool.Audit permissions on /pool/<pool>.
* pveapi qemu master snapshot
  * https://{$PVESERVER}:8006/api2/json/nodes/{$PVENODE}/qemu/{$PVEQEMUID}/snapshot
  * Check: ["perm","/vms/{vmid}",["VM.Audit"]]
* pveapi qemu master status
  * https://{$PVESERVER}:8006/api2/json/nodes/{$PVENODE}/qemu/{$PVEQEMUID}/status/current
  * Check: ["perm","/vms/{vmid}",["VM.Audit"]]
* Summary: Pool.Audit + VM.Audit
