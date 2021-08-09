# ToDo list

This list contains items that need to be created, modified or removed

## Feature requests

* Macro configurable autoclose of the trigger for failed tasks. Either close them after a specific amount of time or leave them open to be manually closed.

## Create

* Datacenter template
  * Monitoring
    * [x] Cluster nodes online
    * [x] Tasks
    * [x] Node online
  * Graphs
    * [x] Cluster
  * Triggers
    * [x] API unreachable
    * [x] Tasks failed
    * [x] Node online
* Node template
  * Monitoring
    * [x] CPU
    * [x] Memory
    * [x] Swap
    * [x] Load
    * [x] KSM
    * [x] uptime
    * [x] Load average
    * [x] Root filesystem
    * [x] Smart status
    * [x] SSD wearout
    * [x] Storage ZFS
    * [x] Storage LVM
    * [x] Storage ThinLVM
    * [x] Services
    * [x] Datastores
  * Graphs
    * [x] CPU
    * [x] Memory
    * [x] Swap
    * [x] Load average
    * [x] KSM
    * [x] Uptime
    * [x] Root filesystem
    * [x] SSD wearout
    * [x] ZFS usage
    * [x] ZFS fragmentation
    * [x] Logical Volume usage
    * [x] ThinLVM usage
    * [x] Datastores
  * Triggers
    * [x] CPU
    * [x] Memory
    * [x] Swap
    * [x] Root filesystem
    * [x] Smart status
    * [x] SSD wearout
    * [x] ZFS health
    * [x] ZFS free space
    * [x] Thin LVM free space
    * [x] Node services
    * [x] Datastores
  * Screens
    * [x] Overview
    * [x] Disks
    * [x] ZFS
    * [x] LVM
    * [x] Thin LVM
* Qemu template
  * Monitoring
    * [x] CPU
    * [x] Memory
    * [x] Network
    * [x] Disk IO
    * [x] Snapshots
    * [x] Uptime
  * Graphs
    * [x] CPU
    * [x] Memory
    * [x] Network
    * [x] Disk IO
    * [x] Uptime
  * Triggers
    * [x] CPU
    * [x] Memory
    * [x] Run status
    * [x] Snapshot age
    * [ ] Locked system
  * Screens
    * [x] Overview
* LXC template
  * Monitoring
    * [x] CPU
    * [x] Memory
    * [x] Swap
    * [x] Network
    * [x] Disk space
    * [x] Disk IO
    * [x] Snapshots
    * [x] Uptime
  * Graphs
    * [x] CPU
    * [x] Memory
    * [x] Swap
    * [x] Network
    * [x] Disk space
    * [x] Disk IO
    * [x] Uptime
  * Triggers
    * [x] CPU
    * [x] Memory
    * [x] Disk space
    * [x] Run status
    * [x] Snapshot age
    * [x] Locked system
  * Screens
    * [x] Overview
* Documentation
  * [x] Usage of macro's
  * [ ] Image of template hierarchy
  * [ ] Authentication within proxmox
  * [ ] Pool based exclusion of triggers
  * [ ] Snapshot age alerting
  * [ ] Snapshot age alerting exclusion

## Modify/verify

* [ ] Look at user macro context for specific trigger values (https://www.zabbix.com/documentation/4.0/manual/config/macros/usermacros#user_macro_context)
* [ ] Look at trigger dependancies for items with multiple level triggers
* [ ] Graph color scheme
* [ ] Graph number of CPU's in title
* [ ] Snapshot monitoring regex full match
* [ ] Pool monitoring regex full match
* [ ] Datacenter monitoring
  * [ ] Consistency of key naming
  * [ ] Update intervals
* [ ] Node monitoring
  * [ ] Consistency of key naming
  * [ ] Update intervals
* [ ] LXC monitoring
  * [ ] Consistency of key naming
  * [ ] Update intervals
* [ ] Qemu monitoring
  * [ ] Consistency of key naming
  * [ ] Update intervals

## Extend

* [ ] make testpools a variable with default value of 'test'
