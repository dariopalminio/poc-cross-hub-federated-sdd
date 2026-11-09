# Agent Instructions: {{REPOSITORY_NAME}}

<!--
Copy this file to the child repository as AGENTS.md and replace every {{...}} token.
Choose the Backend or Frontend values in the role table below. Remove this note after setup.
This template describes the agreed federation workflow; it does not install Spec Kit,
create automation, or replace the child repository's technology-specific guidance.
-->

## Repository Identity

- **Repository**: `{{REPOSITORY_NAME}}`
- **Repository URL**: {{REPOSITORY_URL}}
- **Federated role**: `{{FEDERATED_ROLE}}`
- **Initiative**: `{{INITIATIVE_ID}}`
- **Local specification**: `{{LOCAL_SPEC_PATH}}`
- **Parent Hub**: `https://github.com/dariopalminio/poc-cross-hub-federated-sdd`
- **Parent specification**: `{{PARENT_SPEC_PATH}}`
- **Parent manifest**: `{{PARENT_MANIFEST_PATH}}`

### Preconfigured Child Roles

| Child repository | `{{REPOSITORY_NAME}}` | `{{REPOSITORY_URL}}` | `{{FEDERATED_ROLE}}` | `{{LOCAL_SPEC_PATH}}` |
| --- | --- | --- | --- | --- |
| Backend producer | `poc-be-federated-sdd` | `https://github.com/dariopalminio/poc-be-federated-sdd` | `backend-producer` | `specs/001-health-endpoint/spec.md` |
| Frontend consumer | `poc-fe-federated-sdd` | `https://github.com/dariopalminio/poc-fe-federated-sdd` | `frontend-consumer` | `specs/001-health-page/spec.md` |

For another initiative, replace the example initiative and local specification paths with
the corresponding `links-manifest.yaml` values. Do not copy the table's example initiative
paths into another feature without confirming its manifest entry.

## Project Purpose and Boundaries

This repository implements its assigned contribution to a cross-repository initiative.
The Hub owns the parent business specification and coordinates scope. This child owns its
local specification, technical plan, tasks, implementation, and local verification.

- Do not move product source code, local plans, or local tasks into the Hub.
- Do not redefine cross-cutting requirements or another repository's contract locally.
- Propose cross-cutting scope changes in the Hub parent specification first.
- Record repository-specific architecture, tools, and verified commands in this repository's
  documentation. Do not assume tools or commands that are absent from its manifests and CI.

## Parent-to-Child Specification Contract

The parent initiative is the source of truth for cross-repository requirements. Before
editing or deriving the local specification, resolve `{{PARENT_SPEC_PATH}}` and
`{{PARENT_MANIFEST_PATH}}` at the same immutable Hub commit.

The parent `links-manifest.yaml` entry for this repository and role defines:

- `repo`: this child repository's canonical URL.
- `role`: this repository's assigned contribution.
- `local_spec`: the location of this child specification.
- `scope`: the parent requirement IDs assigned to this child.

The child MUST keep `origin.yaml` beside its local `spec.md`. It MUST contain the following
fields, consistent with the Hub constitution:

```yaml
initiative_id: "{{INITIATIVE_ID}}"
hub_repo: https://github.com/dariopalminio/poc-cross-hub-federated-sdd
spec_source: "{{PARENT_SPEC_PATH}}"
source_revision: "{{FULL_HUB_COMMIT_SHA}}"
role: "{{FEDERATED_ROLE}}"
requirements:
  - "{{PARENT_REQUIREMENT_ID}}"
```

Replace every example token with a concrete value before declaring the child aligned. The
`source_revision` MUST be the full commit SHA containing both the parent `spec.md` and its
`links-manifest.yaml`. A branch name, tag that can move, or synchronization timestamp is not
a substitute for that SHA.

Before proceeding, verify all of the following against that exact parent revision:

1. `initiative_id` matches the manifest `id`.
2. `hub_repo`, `spec_source`, `role`, and this repository's URL and local spec path resolve
   to the matching manifest entry.
3. `requirements` matches that entry's `scope`; every listed ID exists in the parent spec.
4. The child spec preserves those IDs and their applicable acceptance criteria and
   integration dependencies.
5. Every referenced shared contract resolves to the exact owner, path, and immutable
   revision declared by the parent manifest.

An unresolved path or SHA, missing requirement, or metadata mismatch blocks any claim that
this child is aligned. Do not silently guess a parent revision or edit `origin.yaml` merely
to make the checks appear to pass.

## Contract Ownership

The Backend repository, `poc-be-federated-sdd`, is the canonical owner of the OpenAPI
contract consumed by the Frontend.

- **Backend producer**: define and maintain the canonical contract in the Backend repository.
  Record its owner repository, path, and full commit SHA in the Hub manifest when the
  contract is included in the initiative. Publish changes as a new immutable revision;
  coordinate breaking changes with consumers and document a migration plan.
- **Frontend consumer**: consume the exact contract revision declared by the Hub. Do not
  edit or publish an independent authoritative copy. Raise required interface changes to
  the Backend owner and update this child's origin only when adopting an agreed parent
  revision.

For initiatives without a shared contract, the parent manifest MUST use an explicit empty
`shared_contracts: []`. Do not claim contract alignment before an immutable revision exists.

## Local Spec Kit Workflow

Each child repository maintains its own Spec Kit installation and constitution. After the
parent-derived local `spec.md` and `origin.yaml` pass the checks above:

1. Use `/speckit-clarify` for local ambiguities. If an answer changes cross-cutting scope,
   update the parent initiative first instead.
2. Use `/speckit-plan` to plan the local contribution under this repository's constitution
   and the pinned shared contracts.
3. Use `/speckit-tasks` to produce the local task breakdown from the reviewed plan.
4. Use `/speckit-implement` to implement only this repository's assigned contribution.
5. Run checks documented by this repository's dependency manifests and CI. Report checks
   that could not be run; do not describe them as passed.

These commands do not imply that custom derivation commands, receipt generation, remote
dispatch, or other cross-repository automation are installed.

## Resynchronization and Delivery

When the parent spec, manifest, or a shared contract changes:

1. Compare the new immutable revision with `origin.yaml` and identify affected local
   requirements, plans, tasks, and implementation.
2. Do not report alignment with the new revision until this child has reviewed and updated
   its affected derived artifacts.
3. Update `origin.yaml` in the same child change that adopts the new parent revision.
4. Keep evidence for prior revisions; do not rewrite historical results as if they were
   produced against the new revision.

A local completion report MUST identify the implementation commit, parent source revision,
shared contract revisions, covered requirement IDs, checks and their results, and any
limitations. Local completion is not completion of the cross-repository initiative. The Hub
can close the initiative only after all linked contributions and cross-cutting acceptance
criteria, including integration criteria, have evidence.

## Verification Guidance

- Inspect the child specification, `origin.yaml`, the matching parent manifest entry, and
  all referenced revisions when reviewing a change.
- Obtain install, test, lint, build, and type-check commands from actual project manifests
  and CI configuration. Do not invent commands when those files do not exist.
- Verify OpenAPI compatibility against the pinned contract when a project-supported
  validation tool is configured; record unavailable validation as a limitation.
- Report requirement traceability, contract compatibility, integration evidence, and
  unexecuted checks separately.

## Repository-Specific Guidance

<!-- Add verified local architecture, security, testing, and toolchain instructions here. -->
