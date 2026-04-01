# Storage Design

## Objective
This document records the storage considerations for the Phase 1 build.

## Key Areas
- Physical server disk configuration
- RAID visibility during installation
- Proxmox installation target
- VM disk placement
- Template and clone storage consumption
- snapshot storage awareness

## Practical Notes from Build
During the server setup process, storage visibility and boot behavior required careful attention. RAID/controller presentation affected whether the installer and BIOS could properly detect usable storage.

This highlighted a key operational lesson:
hypervisor installation is not only about installing software; it also depends heavily on correct storage/controller presentation at the hardware level.

## Design Considerations
- Keep base OS installation clean and stable
- Avoid unnecessary storage complexity early
- Track where VM disks are stored
- Be aware that full clones consume more storage than linked clones
- Use snapshots deliberately rather than excessively