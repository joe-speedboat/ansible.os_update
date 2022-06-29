Role Name
=========

This role will aply security or full patching to Red Hat, Ubuntu and Alpine machines.

It can do 
* security or full patching
* cleanup old kernel versions
* detect needed reboots and boot them (see vars)

Since I only work with AWX (v20.0.1), it is only tested with its (latest) ansible versions.

Requirements
------------

Working internet and proper repository configuration on machines.

Supported Operating Systems:
----------------------------
* RHEL, CentOS, Rocky, Alma
  * Version: 6-9 

* Ubuntu LTS: 18.04 20.04

* Alpine: stable-latest

* Debain should work, I don't use it for my enterprise aware setups.

* Mint works, but has no focus


Role Variables
--------------

* `ospatch_level:` security   
ospatch_level: [none|security|full]

* `ospatch_reboot:` true   
ospatch_reboot: [true|false]

* `ospatch_remove_old_kernel:` true   
ospatch_remove_old_kernel: [true|false]

* `ospatch_keep_kernel_nr:` 2


Dependencies
------------
None so far.

Example Playbook
----------------

check test dir for examples

License
-------

GPLv3

