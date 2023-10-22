# Notes

This part contains some notes which might be useful when interpreting values in
zabbix.

## Zabbix versions

I currently run the most recently released zabbix verions in production. With version 5.4
the expression syntax had a rather big change. Therefore it is not trivial to import the
template on the latest LTS (5.0) of Zabbix. Starting with the new LTS release (6.0) I will
try to setup a zabbix development server which I will keep at the latest LTS release, so
the template will be available for LTS.

## Nodes

Nodes that have disks where the wearout (normally used for SSD's) is not available,
the wearout is hard set to 100% (no wearout). This also effectively disables any
triggers for the wearout.
