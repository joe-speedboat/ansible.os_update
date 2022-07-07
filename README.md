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

* Debain should work, I only use LTS distributions, which can have SLA.

* Mint works, but has no focus (personal needs)


Role Variables
--------------
* gather_facts is turned off, due we need to decide if host is online first.    
  we do this multiple times, to avoid load and connectivity issues.   
  please look into `tests` folder.   


Most variables have ```varname_default``` equivalent, which is meant to be used for overriding the default at playbook level.   
So you can define the default behavior for all targets that have no variables defined at all. eg: full|security patching.   

Let's make an example:
* default ```ospatch_reboot``` in ```defaults/main.yml``` is set to ```True```
* in playbook, you define ```ospatch_reboot_default``` with ```False```
* in inventory, you have set ```ospatch_reboot``` to ```True``` for your hostgroup ```testing```
So all your host will avoid reboot after patching, except your hostgroup ```testing``` .... voilà, clever, isn't it?   

Remind: `varname` is always enforcing, `varname_default` is just overriding the roles default behavior.

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

