# ToDo list

This list contains items that need to be created, modified or removed

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
    * [ ] Datastores
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
    * [ ] Datastores
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
    * [ ] Datastores
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
    * [ ] Locked system
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
