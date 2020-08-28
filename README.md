LVM Ansible role
================

Role to manage LVM Groups/Logical Volumes. Can be used to create, extend or resize LVM Groups and volumes. To avoid managing
devices list on inventories, creation of VG is now separate from managing (resizing, mounting LV, etc.)

Requirements
------------

No more need for devices/disks to be part of the LVM setup. Now use `lvm_action` instead.

Role Variables
--------------

`lvm_apply` allow backward compatibility for non-LVM
`lvm_groups` is a list containing vgs.
`lvm_action` is used for VG creation/upgrade

### vgs

- `vgname`: uniq name
- `disks`: add disks/partitions to vg (comma separated)
- `state`: present (-> create), absent (-> remove)
- `lvnames`: lv list (see bellow)

### lvnames

- `lvname`: uniq name
- `size`: define size of lvol (ex: "10G", "512M"...)
- `status`: defines if lvol should exist, mounted, exported or be removed...Values are mounted,inactive,absent
- `filesystem`: defines filesystem to format lvol as
- `mount_point`: defines mountpoint
- `mount_options`: defines mount options (comma separated)
- `lvm_owner`: defines lvm owner (default to root)
- `lvm_group`: defines lvm group (default to root)

Dependencies
------------

None

Example Playbooks
-----------------

Create a new VG named `new_vg` with two already attached disks, `/dev/sdc` and `/dev/sdd`.

```
    - hosts: servers
      vars:
        lvm_action: "add_vg"
        lvm_vgname: "new_vg"
        lvm_disk:
	  - /dev/sdc
	  - /dev/sdd
      roles:
         - peopledoc.ansible-lvm
```

Apply configuration with multiple LV on the VG named `misc-vg`

```
    - hosts: servers
      vars:
        lvm_apply: true
        lvm_groups:
          - vgname: misc-vg
            state: present
            lvnames:
              - lvname: swap_1
                size: 5g
                state: mounted
                filesystem:
              - lvname: mysql
                size: 40g
                state: inactive
                filesystem: ext4
                mount_point: /var/lib/mysql
                mount_options: 'defaults,noatime'
		lvm_owner: looker
		lvm_group: admins
          # VG whitout LV
          - vgname: test-vg
            state: present
            lvnames: []

      roles:
         - peopledoc.ansible-lvm
```

License
-------

LGPL

Donation
--------

 :star: the project is also a way of saying thank you! :sunglasses:

Author Information
------------------

- Current fork by [François TOURDE](https://github.com/FrancoisT31) for [PeopleDoc](https://www.people-doc.com)
- Based on [HanXHX/ansible-lvm](https://github.com/HanXHX/ansible-lvm) Twitter: [@hanxhx_](https://twitter.com/hanxhx_)
