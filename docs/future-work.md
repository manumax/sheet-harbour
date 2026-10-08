# SheetHarbour future work

This document records work intentionally deferred from the local MVP. It keeps future-facing ideas visible without making them accidental requirements for the first implementation.

The local MVP is a developer-machine-testable TypeScript/React application with SQLite persistence, no cloud credentials, a stable local application URL, one Sheet Template per Table, immutable Sheet Template Versions, structured Character Sheets, simple GM Links and Player Links, one active browser, and last-write-wins behavior. **Nothing in this document is part of that local MVP.** A future item needs its own product decision, threat/model review where relevant, acceptance criteria, and independently reviewable PR sequence before implementation.

Terminology in this document follows [GLOSSARY.md](../GLOSSARY.md): Table, Player Slot, Character Sheet, Sheet Template, Sheet Template Version, GM Link, and Player Link. Product boundaries come from [README.md](../README.md); interaction expectations come from [docs/ui-ux-design-brief.md](ui-ux-design-brief.md).

## How to use this roadmap

- Keep deferred behavior out of MVP PRs, including “temporary” database tables, route placeholders, UI controls, or copy that implies the behavior already exists.
- Treat prerequisites as gates, not as permission to implement the future feature early.
- Split each future item at the proposed boundaries so documentation, product decisions, schema changes, UI, operations, and migration work can be reviewed separately.
- Revisit the trust model before adding account-grade access, link lifecycle controls, external integrations, or automated data lifecycle behavior.
- Do not infer provider, hosting topology, retention period, email vendor, or Owlbear API contract from this roadmap.

## Deferred work at a glance

| Future area | Why deferred | First prerequisite | Explicit MVP status |
| --- | --- | --- | --- |
| Sheet from Beyond documentation | The local MVP proves the SheetHarbour URL journey but does not define an integration setup guide or support commitment. | Validate the supported one-way handoff and source/version assumptions with contributors. | **Not part of the local MVP.** |
| Managed deployment and production observability | Local SQLite and no credentials keep implementation/test work reproducible; operating a public service is a separate risk and cost decision. | Choose a deployment and data durability direction, including ownership and budget. | **Not part of the local MVP.** |
| Stronger access and link lifecycle | Possession of a link is adequate only for trusted groups and does not provide recovery, rotation, or identity. | Threat model and product decision for identity, recovery, revocation, and migration. | **Not part of the local MVP.** |
| Deletion, backup, restore, and email capabilities | Lifecycle automation and outbound communication create user-safety, retention, privacy, and operations obligations. | Define retention, recovery, consent, delivery, and support policies. | **Not part of the local MVP.** |
| Native Owlbear integration and room binding | The MVP is an external web application with a one-way URL handoff, not a native extension or synchronized room surface. | Integration contract and ownership model with the external platform. | **Not part of the local MVP.** |
| Formulas and rules automation | Automation can change the meaning of structured fields and requires RPG-system-specific product design. | Define supported systems, expression safety, and version/migration rules. | **Not part of the local MVP.** |
| Images and richer media | Media introduces storage, moderation, performance, and backup concerns not needed for durable text/number sheets. | Define supported media types, limits, storage, and lifecycle. | **Not part of the local MVP.** |
| Collaboration and conflict handling | The MVP intentionally assumes one active browser and last-write-wins. | Define synchronization semantics, conflict UX, and workload/latency goals. | **Not part of the local MVP.** |
| Template authoring, publishing, and migration | MVP templates are contributor-provided and seeded; authoring and migration need governance and compatibility rules. | Define ownership, review, publishing, compatibility, and migration policy. | **Not part of the local MVP.** |

## 1. Sheet from Beyond documentation and setup guidance

### Intent and rationale

The MVP can be opened through a stable SheetHarbour URL and remains compatible with the product direction that Sheet from Beyond, launched from Owlbear Rodeo, can open an external destination. It intentionally does **not** deliver setup or integration documentation. A useful guide requires validated product and platform details, and publishing an incorrect configuration guide would imply support that the MVP does not promise.

This work should document a one-way handoff only. It must not imply that SheetHarbour knows about Owlbear rooms, synchronizes changes back, supplies a native Owlbear extension, or manages an external account.

### Prerequisites

- Confirm the supported Sheet from Beyond and Owlbear Rodeo versions/entry points.
- Confirm the exact stable HTTPS SheetHarbour URL shape that a GM should configure, without replacing ordinary application routes with temporary preview links.
- Validate the user journey and wording with the Sheet from Beyond maintainers or the product owner.
- Decide which screenshots, troubleshooting guidance, and support boundaries can be maintained.

### Proposed future PR boundaries

1. **Document the validated one-way handoff:** contributor-facing setup and usage guide, terminology review, stable URL examples, and explicit non-goals for synchronization/rooms.
2. **Add lightweight in-app context copy if needed:** a dismissible “opened through Sheet from Beyond” context marker, without a handoff dialog or authorization flow.
3. **Add documentation checks:** link validation, version/update ownership, and a test that the guide does not promise native integration or two-way synchronization.

Every PR in this area remains future work. **Sheet from Beyond setup/integration documentation is not part of the local MVP.**

## 2. Managed deployment and production observability

### Intent and rationale

The MVP is local-first so contributors can test the product without cloud credentials or a deployed service. Running SheetHarbour for real trusted groups introduces deployment, durable hosted storage, secrets, migrations, access to logs, incident response, cost controls, and production privacy obligations. These are operational decisions, not a by-product of adding a local SQLite file.

The README describes an external web application and a low-operations pilot direction, but it does not select a provider or promise a deployment topology. Future work must preserve that distinction and avoid presenting local database behavior as a production backup guarantee.

### Prerequisites

- Approve a deployment target, ownership model, budget, region/data-handling expectations, and service-level expectations.
- Decide how local SQLite data is migrated to hosted durable storage, including schema migration and rollback strategy.
- Define secret management, environment configuration, TLS/HTTPS, domain, link entropy requirements, and access to operational data.
- Define privacy boundaries, retention, incident response, and what production metrics/logs may contain.
- Establish a pre-production environment and a data-safe fixture/seed strategy.

### Proposed future PR boundaries

1. **Production architecture decision:** ADR or equivalent documenting provider choices, data durability, deployment topology, and rejected alternatives.
2. **Hosted persistence migration:** production-safe storage adapter/migrations and a migration tool from supported local fixtures; no UI feature changes in this PR.
3. **Application deployment:** build/package/deploy configuration, HTTPS/domain configuration, environment validation, health/readiness behavior, and rollback procedure.
4. **Production observability:** structured logs with sensitive-link redaction, metrics for availability and save failures, traces only where justified, alert thresholds, dashboards, and runbooks.
5. **Operational rehearsal:** pre-production smoke/E2E checks, migration/rollback rehearsal, cost/usage checks, and a documented release gate.

Managed deployment and production observability are **not part of the local MVP**. Cloud credentials must not be added to the MVP test harness.

## 3. Stronger access and link lifecycle

### Intent and rationale

In the MVP, link possession is the practical access mechanism for trusted groups. A GM Link opens a Table view and a Player Link opens a Player Slot view; neither is an account or identity. That is intentionally simple but leaves open what happens when a link is copied to the wrong person, lost, or needs to be replaced.

Stronger access must be designed rather than inferred. Rotation, recovery, revocation, accounts, or permissions can change the threat model and the meaning of Player Slots. They may also require migration of existing links and a humane unavailable-link flow.

### Prerequisites

- Threat model link disclosure, guessing, browser history, screenshots, logs, and shared devices.
- Decide whether the future model uses link rotation/revocation, account-based identity, recovery contacts, or a combination.
- Define how a GM proves authority to recover or replace a GM Link without an account in the current data.
- Define Player Slot transfer semantics separately from person/account identity.
- Decide token entropy, storage/redaction, expiry, auditability, rate limits, and user-facing warnings.
- Plan migration and invalidation for links created by the local MVP or an initial deployment.

### Proposed future PR boundaries

1. **Access/lifecycle decision:** threat model, terminology, recovery policy, and explicit decision on link rotation versus accounts.
2. **Link storage and verification hardening:** token handling, hashing/redaction, rate limiting, route/error behavior, and migration compatibility.
3. **GM Link lifecycle:** rotate/revoke/recover behavior with confirmation, audit treatment, and a tested impact on Table access.
4. **Player Link lifecycle:** per-slot rotation/revocation/transfer behavior, unavailable-link UX, and tests that other Player Slots remain isolated.
5. **Accounts or stronger identity, if approved:** registration/sign-in/recovery and association rules as a separate product, security, and migration sequence—not as an add-on to link work.

Link rotation, link recovery, account identity, and stronger access controls are **not part of the local MVP**. The MVP must not add lifecycle buttons that do nothing or imply a recovery promise.

## 4. Deletion, backup, restore, and email capabilities

These capabilities are grouped because each changes the data-lifecycle and support contract, but they should remain separate implementation PRs.

### 4.1 Deletion and lifecycle controls

**Rationale:** The MVP intentionally excludes automated deletion, expiration, cleanup, and deletion recovery. Destructive behavior requires clear ownership, confirmation, retention, audit, and recovery expectations. A Player Slot is not an account, so “delete user” is not a safe inferred operation.

**Prerequisites:** Define who can delete a Table, Player Slot, or Character Sheet; whether deletion is soft or permanent; retention and legal/privacy expectations; how links behave afterward; confirmation and audit requirements; and what happens to a Character Sheet's Sheet Template Version references.

**Proposed future PR boundaries:**

1. Lifecycle policy and domain decision, including object-level ownership and retention.
2. Non-destructive/manual deletion domain and repository behavior with authorization and audit tests.
3. User-facing deletion flows and unavailable-link states, with explicit warnings and keyboard/screen-reader coverage.
4. Optional automated retention/cleanup job with dry-run, monitoring, and rollback/recovery procedures.

Deletion automation and deletion/recovery UI are **not part of the local MVP**.

### 4.2 Backups and restore

**Rationale:** A local SQLite file and test fixture provide persistence for development, not a backup product promise. Hosted backup strategy depends on the selected deployment/storage system and needs restore testing to be meaningful.

**Prerequisites:** Choose production storage, define recovery point/recovery time objectives, retention and encryption requirements, operator permissions, sensitive-data handling, and restore ownership.

**Proposed future PR boundaries:**

1. Backup/restore decision and threat/privacy review.
2. Automated backup configuration and retention with redaction/access controls.
3. Restore tooling into an isolated environment, including schema/version compatibility checks.
4. Restore drill, alerting, and runbook with evidence that links and Sheet Template Versions remain interpretable.

Backups, restore, and archival are **not part of the local MVP** and must not be advertised merely because SQLite persists locally.

### 4.3 Reminder email and email workflows

**Rationale:** The MVP intentionally avoids email delivery, invitations, reminders, and email capture. Email creates consent, deliverability, provider credentials, personal-data, unsubscribe, and support obligations. Player Links are intentionally shared directly by the GM in the trusted-group model.

**Prerequisites:** Define the user value and trigger, consent and recipient model, privacy policy, sender identity, delivery provider, bounce/unsubscribe behavior, link exposure policy, and operational ownership.

**Proposed future PR boundaries:**

1. Email product and consent decision, including whether emails ever contain or point to a Player Link.
2. Provider adapter and delivery observability in isolation from UI invitations.
3. Template/preferences/unsubscribe handling with accessibility and localization review.
4. Reminder/invitation UI and end-to-end delivery tests using a local fake provider.

Reminder email, invitation email, and email capture are **not part of the local MVP**. No email credentials belong in local setup.

## 5. Native Owlbear integration and room binding

### Intent and rationale

SheetHarbour is an external web application in the MVP. Sheet from Beyond can provide a one-way handoff to a SheetHarbour URL from Owlbear Rodeo, but SheetHarbour does not bind a Table to an Owlbear room, receive room state, provide a native extension, or synchronize changes back.

A native extension or room binding would create a separate integration product with platform permissions, lifecycle events, identifiers, failure modes, and likely a revised access model. It must not be inferred from the existence of a stable URL.

### Prerequisites

- Confirm a supported Owlbear integration surface and API/SDK contract.
- Define whether a Table maps to a room, a token, or another external object, and how that mapping is created/recovered.
- Define ownership, permission, disconnect, deletion, and two-way synchronization semantics.
- Decide whether Sheet from Beyond remains the handoff or becomes a separate integration surface.
- Threat-model external identifiers and room access.

### Proposed future PR boundaries

1. Integration architecture and platform contract ADR.
2. Native extension shell and permission/error handling, with no sheet synchronization yet.
3. One-way Table/room association and binding lifecycle.
4. Read/write synchronization contract, conflict behavior, and user-facing status.
5. Production pilot and support documentation.

Native Owlbear integration, room binding, and two-way synchronization are **not part of the local MVP**.

## 6. Formulas, rules automation, and RPG-system behavior

### Intent and rationale

The MVP keeps Character Sheets close to pen and paper: structured fields are rendered and persisted, but formulas and rules automation are excluded. Automation can make field values depend on one another, create validation and explainability requirements, and change how Sheet Template Versions remain interpretable.

### Prerequisites

- Choose supported RPG/game systems and define what “automation” means for each.
- Define a safe expression model, dependency graph, evaluation limits, error behavior, and test strategy.
- Decide whether computed values are stored, derived, or both, and how old Sheet Template Versions evaluate.
- Define migration and user override semantics when rules change.

### Proposed future PR boundaries

1. Rules/automation product decision and domain model.
2. Safe expression/evaluation engine with limits and exhaustive tests, independent of UI.
3. Template schema extension for computed fields and versioned compatibility.
4. Renderer/editor presentation for calculated values, explanations, and validation.
5. RPG-system-specific rule packs, each separately reviewed.

Formulas, rules automation, and a rules engine are **not part of the local MVP**.

## 7. Images and richer media

### Intent and rationale

Images are not required to prove durable structured Character Sheets. They introduce storage, upload security, file limits, resizing, content moderation, performance, backup, and deletion questions.

### Prerequisites

- Define supported media types, size/dimension limits, moderation and abuse response, privacy, and lifecycle behavior.
- Choose storage and delivery architecture as part of managed deployment work.
- Define how media is referenced by a Sheet Template Version and deleted with its owning object.
- Provide accessibility requirements for alt text, captions, and non-visual editing.

### Proposed future PR boundaries

1. Media product/abuse/accessibility decision.
2. Storage/upload adapter with local fake implementation and validation limits.
3. Template field and renderer support for media metadata and accessible alternatives.
4. User-facing upload/remove behavior, lifecycle integration, and production delivery.

Images, avatars, uploads, and media fields are **not part of the local MVP**.

## 8. Multi-device synchronization and conflict handling

### Intent and rationale

The MVP assumes one active browser and explicitly uses last-write-wins. That keeps the editor and autosave understandable for trusted groups while avoiding a false promise of collaborative editing. Users may eventually need to work from multiple devices or recover from overlapping edits, but the correct behavior requires product design and technical investigation.

### Prerequisites

- Gather real usage evidence about simultaneous or multi-device editing.
- Define whether the goal is presence, draft separation, merge assistance, revision history, or real-time collaboration.
- Choose conflict semantics and user language that do not hide data loss.
- Define latency, offline behavior, reconnect behavior, audit/history, and storage costs.

### Proposed future PR boundaries

1. Synchronization requirements and conflict UX decision, including a migration path from last-write-wins.
2. Revision/history or change-log persistence, if selected.
3. Client/server synchronization protocol and stale-write detection.
4. User-facing conflict/recovery UI with accessible explanations and tests.
5. Optional real-time presence/collaboration, only if explicitly approved.

Multi-device conflict resolution, merge UI, real-time synchronization, and presence are **not part of the local MVP**.

## 9. Sheet Template authoring, publishing, and migration

### Intent and rationale

The MVP uses a contributor-provided immutable registry and seeded Sheet Template Version. It proves that Character Sheets can remain interpretable against the version that defines them. It does not provide an authoring UI, publishing workflow, compatibility checker, or automatic migration.

### Prerequisites

- Define who owns a Sheet Template and who can review/publish a Sheet Template Version.
- Define schema validation, naming/versioning, deprecation, compatibility, and release review.
- Decide how Tables choose a version and how a new version is offered to existing Tables or Character Sheets.
- Define migration safety, preview/rollback, user consent, and handling of incompatible fields.

### Proposed future PR boundaries

1. Contributor authoring format, validation CLI, and review checklist.
2. Registry publishing/version governance and compatibility metadata.
3. Table/Character Sheet version selection and upgrade presentation.
4. Migration engine with dry run, per-version tests, and rollback.
5. Optional authoring UI, only after the contributor workflow is stable.

Template authoring, publishing, automatic migration, and replacement workflows are **not part of the local MVP**.

## 10. Additional explicitly deferred product surfaces

The following are also out of scope unless a later product decision creates a focused item and PR sequence:

- **Accounts and profiles:** A Player Slot is not a person or account identity in the MVP. Account-based identity requires its own access and migration design.
- **Link recovery and full revocation:** The MVP intentionally does not promise recovery, rotation, expiry, or revocation.
- **Search, favorites, sharing controls, chat, notifications, and analytics:** The MVP's My Sheets and Players surfaces remain deliberately small.
- **Automatic cleanup or expiration:** No job should delete or expire Tables, Player Slots, or Character Sheets as an assumed maintenance feature.
- **Data export/import:** Structured data may make this possible later, but format, privacy, version, and access decisions are required first.
- **Localization and broad content customization:** Copy, relative time, validation, and template semantics need an explicit internationalization plan before being generalized.
- **Offline-first editing:** Local persistence in the development environment is not an offline-sync promise. Offline queues and reconciliation require their own reliability model.
- **Advanced audit/history:** Last-write-wins is sufficient for the MVP; revision history and audit views require retention and privacy decisions.

Each item above is **not part of the local MVP**. Do not create visible controls, database structures, or documentation that imply otherwise.

## Re-entry gate for future work

Before a deferred item moves into implementation, its proposal should answer:

1. What user problem and success criterion justify it?
2. Which canonical domain terms and existing MVP invariants does it preserve or change?
3. What threat, privacy, data-lifecycle, accessibility, and operational risks does it introduce?
4. What are the prerequisites and migration implications for existing Tables, Player Slots, Character Sheets, Links, and Sheet Template Versions?
5. What is the smallest independently reviewable PR sequence, and which concerns must remain separate?
6. Which local tests and, if applicable, managed-environment tests prove the behavior?
7. What documentation, support, and rollback/runbook obligations accompany the feature?

Until those answers are approved, contributors should keep the behavior in this document as deferred roadmap work rather than implementing it in the local MVP.
