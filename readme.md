# Configuration backups from an environment w/o direct access

## Config gathering

Any device specific role generates {{ config_current }} and {{ config_latest }} varaibles for role backup, which checks configuration changeand does local configuration backup tasks on config change event.

Task name: Cisco-backups
Backup Cisco devices.

Task name: Juniper-backups
Backup Juniper Junos (SRX) devices.

Any additional device type can be added.

## Backups

Backups are done only when a difference from the latest saved configuration are noticed.
{{ config_changed }} event generates a new configuration backup with date of creation and updates the latest configuration backup.
This allows tracking configuration changes and prompt recovery with the latest saved configuration.
Local backups are kept in {{ backup_path }} from inventory file variables
In order to monitor backup task activity, job log for each backed up device is made separately.
Local backup job log is kept in {{ joblog_path }} from inventory file variables

## Remote backups

Role remote_backup works with items from hosts group [backup].
Remote backup server is in group [bak].
Remote conftiguration save is only done once {{ config_changed }} event is noticed.
Remote backups are kept in {{ remote_backup_path }} from inventory file variables
Backup job log is kept in {{ remote_joblog_path }} from inventory file variables.

Task name: Remote-backup
Store configuration backups on remote linux machine.

## Topology
                                                              +
                                                            + |
                                                            | |
                                           +---------+      + |             +---------+
                                           |         |        |             |         |
                                           | Local   |        |             | Remote  |
         +---------+                       | Backups |      + |             | Backups |
         |         |    Network_cli        |         |      | |             |         |
         |  nxos   | <---------------+     +---------+      + |             +---------+
         |         |                 |       ^                |               ^
         +---------+                 |       |                |               |
                                     |       |              + |               |
                                   +-----------+            | |     +-----------+
         +---------+               |           |            + |     |           |
         |         |    Netconf    |  Ansible  |    SSH       |     |  Remote   |
         |  junos  | <-------------+           +------------------> |  Linux    |
         |         |               |  Server   |              |     |  Machine  |
         +---------+               |           |            + |     |           |
                                   +-----------+            | |     +-----------+
                                     |                      + |
         +---------+    Ansible      |                        |
         |         |    Method       |                        |
         |  other  | <---------------+                      + |
         |         |                                        | |
         +---------+                                        + |
                                                              +

## Variables

All neccessary variables are kept in the inventory file and shared by roles.
List of variables:
  backup_path
  joblog_path
  remote_backup_path
  remote_joblog_path
  jobtime
