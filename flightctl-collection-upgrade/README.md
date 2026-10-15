# Flightctl Collection Upgrade Planning

Plan the work needed for `flightctl.core` to support a newer Flightctl API.
The workflow compares the API baseline represented by the collection with a
target stable API version, maps API changes to existing collection behavior,
and produces a prioritized implementation and validation plan.

## Usage

```text
/flightctl-collection-upgrade:plan [from=<service-tag>] [to=<service-tag>] [collection=<path>] [flightctl=<path>]
```

If versions or paths are omitted, the workflow discovers them from the current
workspace and official versioned sources. It asks when the comparison cannot be
established reliably. `from` and `to` refer to Flightctl service release tags
unless a resource schema `apiVersion` is explicitly requested. The report keeps
service tags, collection API support claims, Python client versions, and schema
versions separate.

## Output

The report is saved in the collection at:

```text
.artifacts/flightctl-collection-upgrade/{from}-to-{to}/upgrade-plan.md
```

The report separates target-version work from pre-existing API gaps and records
the evidence, collection coverage, likely files, and validation for each item.

## Scope

This workflow is planning-only. It does not modify collection code, tests,
dependencies, CI, release metadata, or changelogs; run tests; create Jira
tickets; or publish a release.
