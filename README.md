# joe-speedboat.os_update

This role will aply security or full patching to Red Hat, Ubuntu and Alpine machines.

It can do 
* security or full patching
* cleanup old kernel versions
* detect needed reboots and boot them (see vars)

Since I only work with AWX (v20.0.1), it is only tested with its (latest) ansible versions.

## Requirements

Working internet and proper repository configuration on machines.

## Supported Operating Systems:
* RHEL, CentOS, Rocky, Alma
  * Version: 8-9 

* Ubuntu LTS: 22.04, 24.04

* Alpine: stable-latest

* Debain should work, I only use LTS distributions, which can have SLA.

* Mint works, but has no focus (personal needs)

## Breaking Changes
* The `os_update_reboot` variable has been updated. 
  Old boolean values (`True`|`False`) are no longer supported and should be replaced 
  with the new string values (`allow`|`deny`|`force`).
* however, at the moment we catch them up and migrate old var settings:
  * `True` => `allow`
  * `False` => `deny`

* gather_facts is now turned on by default
  * we notify if missing

## Role Variables
Most variables have ```varname_default``` equivalent, which is meant to be used for overriding the default at playbook level.   
So you can define the default behavior for all targets that have no variables defined at all. eg: full|security patching.   

Let's make an example:
* default ```os_update_reboot``` in ```defaults/main.yml``` is set to ```True```
* in playbook, you define ```os_update_reboot_default``` with ```False```
* in inventory, you have set ```os_update_reboot``` to ```True``` for your hostgroup ```testing```
So all your host will avoid reboot after patching, except your hostgroup ```testing``` .... voilà, clever, isn't it?   

Remind: `varname` is always enforcing, `varname_default` is just overriding the roles default behavior.

* `os_update_level:` security   
os_update_level: [none|security|full]

* `os_update_reboot:` allow   
os_update_reboot: [allow|deny|force]

* `os_update_remove_old_kernel:` true   
os_update_remove_old_kernel: [true|false]

* `os_update_keep_kernel_nr:` 2


## Dependencies
None so far.

## Example Playbook

check test dir for examples

## License

GPLv3

