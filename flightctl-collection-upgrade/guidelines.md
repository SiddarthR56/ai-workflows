# Flightctl Collection Upgrade Planning Guidelines

## Purpose

Compare the API version currently represented by the `flightctl.core` Ansible
collection with a target Flightctl API version. Identify what the collection
already supports, what the API added or changed, and what work would close the
gap.

## Principles

- Discover the collection structure and API surface from the checked-out code;
  do not assume module names or endpoint coverage.
- Support every finding with a source path, API method/path, schema symbol, or
  release-note reference. Mark unknowns and confidence explicitly.
- Keep target-version additions separate from older API gaps that predate the
  target release.
- Treat API fields, resource kinds, queries, and operations as different kinds
  of collection work.
- Recommend the smallest collection change that exposes the API usefully to
  Ansible users.

## Version selection

- Use explicit `from` and `to` versions supplied by the user when present.
- Keep these version streams distinct in the snapshot and report:
  - Flightctl service release tag, such as `v1.3.1`.
  - Collection compatibility claim, such as “Flight Control API 1.3”.
  - Python client package version, such as `flightctl-client==1.3.0`.
  - Per-resource schema versions, such as `flightctl.io/v1beta1` and
    `imagebuilder.flightctl.io/v1alpha1`.
- Unless the user explicitly asks about a schema `apiVersion`, interpret `from`
  and `to` as Flightctl service release tags. Never compare a server tag, SDK
  version, collection version, and schema `apiVersion` as if they were the same
  version stream.
- Infer the service baseline from the collection's integration CI server ref,
  then cross-check its API compatibility claim, client dependency pin, and
  release notes. Report any disagreement.
- Prefer an explicit target. If omitted, use the latest stable Flightctl
  service release verifiable from a local source checkout or official release
  sources. Do not use `-rc`, `-main`, or a development branch as stable. Compare
  API definitions and behavior from the collection's baseline server tag to
  that target; use the previous stable tag only when no collection baseline is
  recorded.
- If the baseline or target cannot be established reliably, ask for the
  missing version or source before comparing. Report conflicting version
  claims instead of choosing one without explanation.

## Source order

For the API delta, use the best available sources in this order:

1. The target and baseline OpenAPI/API specifications or source definitions.
2. Official Flightctl release notes and versioned source history.
3. The Flightctl Python client models and changelog at the matching versions.
4. User-provided API diffs or documentation.

Record exact versions and source locations. If only client models are available,
say that server behavior was not independently verified.

## Collection coverage

Inspect at least:

- `galaxy.yml`, compatibility statements, dependency pins, changelogs, and CI
  product/API refs.
- Modules in `plugins/modules/` and shared module utilities, especially
  `ResourceType`, `API_MAPPING`, `KIND_OPERATIONS`, and equivalent maps.
- Module options and info/query flags, inventory and connection plugins, and
  specialized action modules.
- Unit and integration tests for the relevant modules and plugins.

For `flightctl.core`, check generic resource and resource-info coverage as well
as specialized certificate, enrollment, image-builder, inventory, and console
connection behavior. The names and implementation may change; verify them in
the current checkout.

## Classification rules

- New resource with CRUD: assess whether it belongs in the generic resource
  module/map or needs a specialized module.
- New non-CRUD operation: assess an action module or a state/action on an
  existing specialized module. Do not model it as a resource kind just because
  it has an API path.
- New read/query endpoint: assess an info-module flag or query option.
- New field, enum, validation rule, or nested schema on an existing resource:
  assess client support, module pass-through behavior, examples, docs, and
  tests; do not propose a new resource kind for a schema field.
- Changed or removed API behavior: identify compatibility, migration, and
  deprecation implications.
- Already covered: name the module, option, operation, or test that covers it.
- Missing from the baseline API too: label it a pre-existing gap, not a target
  release addition.

## Boundaries

- Produce an analysis and plan only. Do not edit implementation, tests,
  dependencies, collection metadata, changelogs, or CI files.
- Do not create Jira tickets or publish artifacts. Ticketing and implementation
  require a separate user request.
- Do not run tests as part of planning. Recommend relevant validation for each
  work item instead.
- Save the report under the collection root at
  `.artifacts/flightctl-collection-upgrade/{from}-to-{to}/upgrade-plan.md`.
  If the collection root cannot be identified, present the report inline and
  ask where to save it.
