# Changelog

## v2.1.11 - 2026-06-26

### Fixed

- Added explicit `none-*` task files for supported platform families so `os_update_level: none` resolves through the normal dispatcher instead of failing the `first_found` lookup.
- Kept `os_update_level: none` visible in output by printing a no-op message for Debian-family, RHEL-family, Alpine, and Windows targets.

### Added

- Added `tests/none.yml` as a minimal example for the no-update mode.
- Added `AGENT.md` with the QA workflow expected for this role, including real-VM validation, cleanup, and secret-safety rules.

### Verified

- Tested on fresh temporary cloud VMs for Ubuntu 24.04, Ubuntu 26.04, Rocky Linux 8.10, Rocky Linux 9.8, and Rocky Linux 10.x.
- Ran the full matrix for:
  - `os_update_level`: `none`, `security`, `full`
  - `os_update_reboot`: `deny`, `allow`, `force`
- All nine matrix cases completed with `rc=0`.
- Final package-manager verification returned `0` on every Linux host.
- Rocky Linux 8 was bootstrapped with Python 3.9 via Ansible `raw` before normal module execution, because its default Python 3.6 is not compatible with modern Ansible controller modules.
- All temporary cloud resources were deleted after validation.
