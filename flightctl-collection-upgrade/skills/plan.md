# Plan a Flightctl API Upgrade

Use this procedure for `/plan` and direct skill invocation.

## Inputs

Accept optional arguments:

- `from=<version>` — Flightctl service release tag to compare from (default).
- `to=<version>` — target Flightctl service release tag (default).
- If the user explicitly names a resource schema version such as `v1beta1`,
  analyze that schema-version change separately from server release tags.
- `collection=<path>` — `flightctl.core` collection root.
- `flightctl=<path>` — Flightctl source checkout or API-spec root.

Discover omitted paths from the workspace. Ask only for a version or source that
cannot be established reliably. Do not pause for confirmation when the versions
and sources are clear.

## Procedure

1. **Locate the collection.** Find the `flightctl.core` root using `galaxy.yml`
   and confirm its namespace and collection name. If there are multiple
   candidates, ask which one to analyze.
2. **Establish the baseline.** Read the collection version and keep separate
   values for the Flightctl server release tested in integration CI, the API
   compatibility claim, the Python client pin, and each resource's schema
   `apiVersion`. Cross-check `galaxy.yml`, README, dependency manifests,
   changelog, API maps, and CI. Report discrepancies.
3. **Establish the target.** Unless the user specifies a schema `apiVersion`,
   treat `from` and `to` as Flightctl server release tags. Use the explicit
   target, or find the latest stable official release tag; exclude `-rc`,
   `-main`, and development refs. Compare from the collection's CI baseline tag
   to the target. State all four version streams and the exact comparison range
   before analyzing it.
4. **Collect the API delta.** Compare API source/spec files (including
   generated OpenAPI), versioned docs, release notes, and matching client
   models across the two server tags. Inventory added, changed, deprecated,
   and removed resources, operations, query endpoints, fields, enums,
   validation rules, and relevant behavior. Record evidence for each item.
5. **Map the delta to the collection.** Search modules, shared API maps,
   module arguments, plugin behavior, docs/examples, and tests. For each API
   change, decide whether the collection already exposes it, needs a new
   resource/action/query, needs client or pass-through support, needs docs/tests,
   or has a compatibility concern.
6. **Separate target work from older gaps.** Check whether each uncovered item
   already existed in the baseline API. Place it in pre-existing gaps if so;
   do not count it as work introduced by the target release.
7. **Build a dependency-aware plan.** Identify foundation changes first (client
   pin, CI API ref, compatibility claims), then required module/plugin work,
   then docs and tests. Include likely files and recommended validation without
   making edits or running tests.
8. **Write and report.** Save the report at the path specified in
   `guidelines.md`. Give the user a short summary of the main work items,
   blockers, and artifact path.

## Report format

Use this structure:

```markdown
# Flightctl API Upgrade Plan: {from} → {to}

## Summary
- Collection version and claimed API baseline
- Server release tags compared, Python client pin, and resource schema versions
- Target release source
- Main conclusion and material uncertainty

## Collection snapshot
- Collection version; server baseline tag; API compatibility claim; Python
  client package version; per-resource schema `apiVersion`s
- Relevant modules, resource maps, plugins, and tests

## API delta and collection coverage
| API change and evidence | Classification | Current collection coverage | Recommended work | Likely files | Validation |
|---|---|---|---|---|---|

## Prioritized work plan
1. Foundation and dependency updates
2. Required collection behavior
3. Documentation and examples
4. Tests and compatibility checks

## Pre-existing gaps
Older uncovered API capabilities, clearly separated from target-version work.

## Already covered / no collection change
API changes already supported, with the covering module or option.

## Risks, blockers, and open questions
Include client lag, ambiguous or conflicting version claims, breaking changes,
and findings that could not be verified.
```

## Quality gate

Before presenting the plan, verify that every proposed work item maps to an
observed API change, every claimed existing capability maps to collection code,
and older gaps are not mixed with target-version additions. Avoid proposing
Ansible resources for fields or operations that belong on an existing resource
or action module.
