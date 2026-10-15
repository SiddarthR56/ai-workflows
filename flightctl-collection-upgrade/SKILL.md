---
name: flightctl-collection-upgrade
version: 0.2.0
description: >-
  Plans Flightctl API upgrades for the flightctl.core Ansible collection by
  comparing a supported API baseline with a target version and identifying
  collection work. Use when assessing a new Flightctl API release, upgrading
  the collection's API or client support, or planning missing API coverage.
---
# Flightctl Collection Upgrade Planning

Run `/plan` to compare Flightctl service release tags and API contracts, then
produce a collection work plan. `from` and `to` default to service release tags.
For direct skill use, read `guidelines.md` and execute `skills/plan.md`.

This workflow plans only. It does not change collection code, update release
metadata, create Jira tickets, or publish a collection release.
