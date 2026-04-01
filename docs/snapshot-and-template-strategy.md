# Snapshot and Template Strategy

## Purpose
This document defines how templates and snapshots are used in the lab.

## Templates
Templates are used to speed up repeatable VM deployment and maintain consistency.

### Intended Use
- create a clean Ubuntu base image
- fully update it
- clean temporary files
- convert to template
- clone new machines from it

## Snapshots
Snapshots are used before risky changes such as:
- package upgrades
- major network changes
- domain join attempts
- service configuration changes
- kernel or boot changes

## Lessons from Build
Clone/template operations require verification of:
- network interface identity
- MAC address behavior
- DHCP expectations
- guest OS network persistence

A cloned machine may boot successfully but still require guest-level validation before it is considered production-ready in the lab.

## Rules
- snapshot before major change
- name snapshots clearly
- do not keep excessive stale snapshots
- document why a snapshot was taken