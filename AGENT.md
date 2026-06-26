# Agent QA Guide

This file describes how an automation agent should validate changes to this Ansible role before proposing or publishing them.

The goal is practical QA on real systems. A syntax check alone is not enough for this role because package managers, reboot behavior, Python interpreter discovery, and OS-specific task dispatch can only be trusted after execution on representative hosts.

## Safety rules

- Never commit credentials, cloud API tokens, SSH private keys, inventory secrets, real customer hostnames, or temporary lab IP addresses.
- Use placeholder domains such as `example.com` in public documentation.
- Keep temporary inventories, state files, SSH configs, and raw logs outside the repository or ensure they are ignored before committing.
- Label all temporary cloud resources with a clear purpose and run identifier so they can be deleted reliably.
- Always verify cleanup through the provider API after destructive cleanup commands complete.

## Minimum QA matrix

For Linux QA, use fresh disposable VMs for these families when available:

| Family | Example version | Purpose |
|---|---:|---|
| Ubuntu LTS | 24.04 | Debian-family stable LTS path |
| Ubuntu interim/current | 26.04 | Newer Debian-family Python and apt behavior |
| Rocky Linux | 8.10 | Enterprise Linux 8 / Python 3.6 edge case |
| Rocky Linux | 9.x | Enterprise Linux 9 path |
| Rocky Linux | 10.x | Enterprise Linux 10 path |

Windows support is optional unless the change touches Windows-specific tasks. If Windows is tested, use WinRM and verify `win_updates` behavior separately from Linux package-manager behavior.

## Test variables

Run the full role matrix:

```text
os_update_level:  none | security | full
os_update_reboot: deny | allow | force
```

Recommended lab override while testing update/reboot behavior:

```yaml
os_update_remove_old_kernel: false
os_update_retry: 1
os_update_retry_delay: 5
```

This keeps the test focused on update and reboot logic. Kernel cleanup should be tested separately when that code changes.

## Rocky Linux 8 bootstrap

Fresh Enterprise Linux 8 systems normally expose Python 3.6 as `/usr/bin/python3` or `/usr/libexec/platform-python`. Modern Ansible controller modules require a newer target Python.

Before gathering facts or running normal modules on Rocky Linux 8, bootstrap Python 3.9 with `raw` and pin the interpreter:

```bash
ansible -i inventory.ini rocky8 -m raw -a \
  'dnf -y module enable python39 && dnf -y install python39 && /usr/bin/python3.9 --version'
```

Inventory example:

```ini
rocky8 ansible_host=<temporary-ip> ansible_user=root ansible_python_interpreter=/usr/bin/python3.9
```

Do not install a non-existent `python39-libselinux` package. The role's Enterprise Linux 8 task files avoid Python-bound package modules where needed, but fact gathering and generic Ansible modules still need a supported interpreter.

## Execution workflow

1. Pull the current default branch and confirm the working tree is clean.
2. Create fresh disposable hosts for the target matrix.
3. Generate a temporary inventory outside the repository.
4. Verify raw Python versions before bootstrapping.
5. Bootstrap Rocky Linux 8 with `raw` if present.
6. Run Ansible connectivity checks:

   ```bash
   ansible -i inventory.ini all -m ping
   ansible -i inventory.ini all -m setup -a 'filter=ansible_distribution*,ansible_python*'
   ```

7. Run a syntax check for the matrix playbook:

   ```bash
   ansible-playbook -i inventory.ini test-os-update.yml --syntax-check
   ```

8. Run every `os_update_level` / `os_update_reboot` combination.
9. Run final independent verification on every host:

   ```bash
   apt-get check     # Debian-family hosts
   dnf -y check      # RHEL-family hosts
   uname -r
   uptime -s
   ```

10. Delete all temporary VMs and verify through the cloud API that none remain.
11. Summarize only sanitized evidence in changelog or pull-request text: OS versions, kernels, return codes, and cleanup status are useful; IP addresses, hostnames, API tokens, and private inventory details are not.

## Passing criteria

A change is QA-passed only when all of the following are true:

- Syntax check passed.
- Ansible ping and facts succeeded for every target.
- All required matrix cases returned `rc=0`.
- Reboot-enabled cases returned to Ansible connectivity after reboot.
- Final package-manager health checks returned `0`.
- Temporary resources were deleted and cleanup was verified.
- No credentials, provider tokens, private keys, temporary IPs, or internal hostnames appear in committed files.

## Reporting standard

When reporting QA results, include:

- Role commit or branch tested.
- Controller Ansible version/path.
- OS/version matrix.
- Matrix case summary with return codes.
- Final independent verification result.
- Cleanup verification result.
- Known limitations, for example Windows not tested when a change was Linux-only.

Keep the report concise and reproducible. Do not paste full raw logs into public documentation.
