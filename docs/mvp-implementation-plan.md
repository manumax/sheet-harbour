# SheetHarbour local MVP implementation plan

## Purpose

This document turns the approved SheetHarbour MVP into a small, reviewable implementation sequence. It is a contributor-facing plan, not an implementation specification. The implementation should preserve the product language in [GLOSSARY.md](../GLOSSARY.md), the product boundary in [README.md](../README.md), and the interaction and accessibility requirements in [docs/ui-ux-design-brief.md](ui-ux-design-brief.md).

The MVP is a **local, developer-machine-testable TypeScript/React application with SQLite persistence and no cloud credentials**. The local application is the source of truth for development and end-to-end acceptance. Deployment and production operations are deliberately deferred to [future-work.md](future-work.md).

## Goals

The MVP must let a trusted tabletop group demonstrate the complete durable-sheet journey on one developer machine:

- Open the application at a stable normal application URL (the default local URL should be documented and deterministic, such as `http://localhost:3000`).
- Create a **Table** with exactly one **Sheet Template**.
- Give the GM a **GM Link** for that Table.
- Create **Player Slots** and give each slot a **Player Link**.
- Open a Player Link and see the player welcome/My Sheets flow.
- Create more than one named **Character Sheet** for the Player Slot.
- Require a non-empty, non-whitespace **Character Name** before creating a Character Sheet.
- Render structured fields from an immutable, versioned **Sheet Template Version**.
- Read and update a Character Sheet through a calm, accessible editor.
- Persist data in SQLite so it remains after refresh and local application restart.
- Autosave after a debounce, show the exact saving status `Saving your latest change…`, and show relative text such as `Last saved 10 minutes ago` after success.
- Make the one active browser and last-write-wins assumption explicit and deterministic.
- Exercise the journeys locally with unit, integration, and browser-level tests without cloud credentials or external services.

## Non-goals and explicit boundaries

The following are not part of this local MVP and must not be smuggled into a slice as “helpful” scaffolding:

- Deployment, managed hosting, production operations, production observability, or provider selection.
- Cloud credentials, third-party services, email delivery, object storage, or remote databases.
- Sheet from Beyond setup or integration documentation. The product boundary remains a one-way external URL handoff; documentation for configuring that handoff is future work.
- Native Owlbear Rodeo integration, room binding, two-way synchronization, or presence.
- Accounts, sign-in, registration, profiles, password recovery, or account-based identity.
- Formulas, rules automation, a rules engine, or RPG-system-specific automation.
- Images, avatars, uploads, or media management.
- Automated deletion, expiration, cleanup, or deletion recovery.
- Backups, restore, archival, or a backup product promise.
- Reminder email, invitations by email, or any other email workflow.
- Link rotation, link recovery, or a full link-revocation workflow.
- Simultaneous multi-browser collaboration, conflict-resolution UI, merge behavior, or real-time synchronization.
- Unrelated design-system expansion, analytics, notifications, search, favorites, sharing permissions, chat, or roster management.

The local MVP may use simple opaque access-link values because the product assumes trusted groups. It must not describe link possession as account-grade identity or imply that excluded lifecycle features exist.

## Decision gate

Implementation may begin and continue only while all of the following remain true:

1. **Scope gate:** the deliverable is the local TypeScript/React application, SQLite persistence, test harness, and the PR sequence below—not deployment or integration operations.
2. **Domain gate:** the MVP uses one Sheet Template per Table; a Character Sheet belongs to a Player Slot and records the immutable Sheet Template Version that defines it; a Player Link opens a Player Slot view and is not itself a Character Sheet.
3. **Persistence gate:** application behavior is backed by a local SQLite database, not browser-only state, and survives refresh and application restart.
4. **Access gate:** GM Link and Player Link are sufficient for the trusted-group MVP; no account or identity model is introduced.
5. **Editing gate:** structured fields are rendered from versioned data; updates use last-write-wins under the one active browser assumption; no conflict UI is added.
6. **Feedback gate:** autosave is debounced and visible without a Save button, including the exact saving string and relative last-saved text.
7. **Quality gate:** each slice remains independently reviewable, has focused tests, and does not depend on an unfinished broad scaffold except for the explicitly listed true dependencies.
8. **Local gate:** a new contributor can install dependencies, start the application at the documented stable URL, run the test suite, and complete the local end-to-end journeys without cloud credentials.

A product decision is required before inventing behavior for unresolved requirements such as the exact Table fields, Player Slot label policy, template selection presentation, access-link display/recovery policy, or autosave-failure copy. A contributor may choose a minimal implementation default only when it is recorded in the slice and does not expand MVP scope.

## Local setup assumptions

These are implementation assumptions for a repeatable developer-machine workflow; they are not production architecture commitments.

- Use the repository's chosen Node.js LTS version and package manager, recorded by the bootstrap slice in the normal version/lock files. Do not require a globally installed database service.
- Use TypeScript for application and server-side code and React for the user interface. Keep the boundary between UI, domain logic, persistence, and local HTTP/data access explicit.
- Use a local SQLite database file created by the application or test fixture. The path must be configurable for tests and ignored from version control. Tests must be able to use isolated temporary databases.
- Do not read cloud credentials, call cloud APIs, or require network services for application startup or tests. Network access may be needed only to install declared dependencies, not at runtime.
- Provide deterministic scripts for at least development, production build, type checking, unit/integration tests, and browser end-to-end tests. The exact script names may follow the repository convention, but the README must document them.
- Bind the development server to a stable, documented local URL (for example `http://localhost:3000`) rather than a random port or generated preview URL. Routes used by GM Links and Player Links must be ordinary application routes that remain usable after a refresh.
- Seed the immutable Sheet Template registry through code or a local fixture so a new developer and the test suite have at least one usable Sheet Template Version without an admin UI.
- Keep generated database files, browser traces, screenshots, reports, and other proof output out of the repository. Test fixtures should clean up their temporary databases.
- Prefer semantic HTML and accessible controls over UI abstractions that conceal labels, focus order, validation, or status announcements. Follow the UI brief's paper/document direction without adding visual work that changes product scope.
- A local test can represent different access links with separate browser contexts or direct route navigation. It need not create accounts or simulate an external identity provider.

## Dependency graph

The graph below is the minimum ordering. Arrows mean “must be complete enough before”; parallel work is allowed where no arrow exists.

```text
A Bootstrap local app and test harness
└──> B Accessible application shell
     ├──> C Domain model and SQLite persistence
     │    └──> E Create Table and GM Link
     │         └──> F Player Slots and Player Links
     │              └──> G Player welcome, My Sheets, and create Character Sheet
     │                   └──> H Character Sheet read/update/editor
     │                        └──> I Debounced autosave and last-saved feedback
     └──> D Immutable Sheet Template registry and structured renderer
          ├──> E Create Table and GM Link
          ├──> G Player welcome, My Sheets, and create Character Sheet
          └──> H Character Sheet read/update/editor

I ──> J Final responsive and accessibility hardening
C ──> D (registry records use the persistence boundary)
H ──> I (autosave attaches to the editor's update seam)
```

The letters identify the PR slices below. A slice should not absorb work from a later slice merely to make the graph look shorter. For example, the shell should establish landmarks and route seams, but it should not implement Table creation; the persistence slice should establish durable seams, but it should not build every screen.

## Parallel work lanes

After the bootstrap slice, contributors can work in these lanes while preserving the dependency graph:

- **Foundation lane:** application shell, routing, layout landmarks, and accessible loading/error primitives (B).
- **Data lane:** domain types, invariants, SQLite schema/repositories, fixtures, and persistence tests (C).
- **Template lane:** immutable Sheet Template registry, validation, version lookup, and structured renderer contract (D; starts after the persistence boundary is agreed, while B proceeds).
- **GM access lane:** Create Table and GM Link, then Player Slots and Player Links (E → F; starts after B, C, and D seams exist).
- **Player lane:** player welcome, My Sheets, and named-sheet creation (G; starts after F and consumes D).
- **Editor lane:** Character Sheet read/update/editor (H; starts after G).
- **Reliability lane:** debounced autosave, exact status copy, relative last-saved text, and deterministic last-write-wins tests (I; starts after H).
- **Quality lane:** responsive, keyboard, screen-reader, reduced-motion, zoom, and failure-state hardening (J; starts after the end-to-end path exists, with accessibility checks able to begin earlier).

Parallel contributors should agree on route names, domain object names, repository interfaces, and test fixture shape before coding against them. Do not use parallel work as a reason to create duplicate domain models or temporary alternate terminology.

## Small PR sequence

Each slice below is intended to be one independently reviewable PR. The likely paths are seams, not a mandate to create every directory immediately; if the starter selects different paths, preserve the same separation of concerns.

### A. Bootstrap the local app and test harness

**Scope:** Establish the smallest runnable TypeScript/React application, local server boundary, SQLite/test dependencies, deterministic scripts, stable local URL, and isolated test setup. Add no product flow beyond a verifiable health/root response and a test proving the harness starts.

**Likely files/modules:**

- `package.json`, lockfile, `tsconfig*.json`, formatter/linter configuration.
- `src/main.tsx` and the minimal application/server entrypoints.
- `vite.config.*` or the selected build configuration; `vitest.config.*` and `playwright.config.*` (or equivalent).
- `tests/unit/`, `tests/integration/`, `tests/e2e/` fixtures and test utilities.
- `.gitignore` entries for local SQLite files and generated test output.
- `README.md` only if a setup command needs documenting; do not expand product requirements in this slice.

**Acceptance criteria:**

- A clean checkout can install dependencies and start the app at the documented stable local URL without cloud credentials.
- The app has a deterministic build, typecheck, unit/integration test, and browser-test command.
- A test can create an isolated SQLite database or equivalent test database boundary and clean it up.
- The root route/health seam responds deterministically, and the browser harness can reach it.
- No account, cloud, deployment, or product-flow scaffolding is introduced.

**Tests:**

- Typecheck and production build.
- A minimal unit/integration smoke test for startup and isolated database setup.
- A browser smoke test that opens the stable local URL and observes the root landmark.

**Reviewer focus:**

- Does setup work on a fresh developer machine with no hidden services or credentials?
- Are local data and generated artifacts excluded from commits?
- Are test fixtures isolated and deterministic?
- Is the scaffold narrow enough that later PRs can be reviewed by concern?

**True dependencies:** None.

### B. Add the accessible application shell

**Scope:** Add the shared page/document shell, route boundary, semantic landmarks, loading/error/unavailable primitives, basic design tokens, and navigation/context conventions needed by subsequent screens. Do not implement domain actions or sheet fields.

**Likely files/modules:**

- `src/app/App.tsx`, `src/app/routes.tsx`, and route-level layout components.
- `src/ui/shell/`, `src/ui/status/`, `src/ui/feedback/`, and token/style files.
- `src/ui/components/Button.tsx`, `LinkResult.tsx`, or equivalent only when reusable shell primitives are needed.
- Shell and keyboard tests under `tests/unit/` and `tests/e2e/`.

**Acceptance criteria:**

- Pages have a predictable landmark structure, heading hierarchy, focus order, and visible keyboard focus.
- The shell can represent GM and Player context without accounts, rooms, or identity claims.
- Loading, recoverable error, empty, and unavailable states preserve useful context and have accessible names/messages.
- Responsive base layout supports narrow and wide viewports without horizontal scrolling for ordinary content.
- The visual foundation follows the UI brief's calm paper/document direction with readable contrast and no decorative scope expansion.

**Tests:**

- Component tests for landmarks, headings, focusable actions, and status semantics.
- Keyboard browser smoke coverage for entering a page, moving focus, and activating navigation.
- Contrast or automated accessibility checks for the shared shell where tooling supports them.

**Reviewer focus:**

- Are labels, landmarks, focus states, and status announcements real semantics rather than visual approximations?
- Does the shell avoid terms such as account, campaign, session, profile, or room where the glossary says otherwise?
- Is the shell reusable without forcing every template into one visual layout?

**True dependencies:** A.

### C. Establish the domain model and SQLite persistence

**Scope:** Define the core domain records, invariants, migrations/schema, repositories, and local transaction boundary for Table, Player Slot, Character Sheet, Sheet Template, Sheet Template Version, GM Link, and Player Link. Implement persistence without building the UI flows.

**Likely files/modules:**

- `src/domain/` for types, invariants, validation, and domain errors.
- `src/db/` for SQLite connection, migrations, transaction helpers, and repositories.
- `src/server/` or `src/api/` for narrow local data-access handlers/use cases.
- `tests/unit/domain/`, `tests/integration/db/`, and migration fixtures.

**Acceptance criteria:**

- A Table records exactly one Sheet Template selection and the selected/version policy is explicit.
- A Player Slot belongs to one Table and can own multiple Character Sheets.
- A Character Sheet records its Player Slot, structured data, and exact immutable Sheet Template Version.
- GM Link and Player Link records resolve to the intended Table or Player Slot without introducing accounts.
- Character Name validation rejects blank and whitespace-only values at the domain boundary, not only in UI code.
- Updates are ordinary durable writes with last-write-wins semantics; no conflict/merge model is added.
- Repositories can create, read, and update the records needed by later slices and survive process restart.

**Tests:**

- Domain tests for relationships, required Character Name, one-template-per-Table, immutable version references, and invalid references.
- SQLite integration tests for migrations, foreign-key/relationship behavior, persistence across connection reopen, and deterministic last-write-wins update order.
- Repository/use-case tests for resolving GM Links and Player Links.

**Reviewer focus:**

- Are domain terms and ownership relationships faithful to GLOSSARY.md?
- Is structured sheet data versioned and extractable rather than an opaque browser-only blob?
- Is immutability protected by schema/repository boundaries rather than convention alone?
- Does the local schema avoid speculative accounts, formulas, images, deletion jobs, backups, or email tables?

**True dependencies:** A. B's route/data boundary agreement may be used, but no completed product screen is required.

### D. Add the immutable Sheet Template registry and structured renderer

**Scope:** Add a contributor-defined registry containing at least one immutable Sheet Template Version, validation/lookup rules, and a structured renderer contract that maps versioned field definitions to accessible controls. Do not add template authoring or migration tooling.

**Likely files/modules:**

- `src/templates/registry/`, `src/templates/definitions/`, and registry fixture/seed modules.
- `src/templates/validation.ts` and `src/templates/types.ts`.
- `src/features/sheets/StructuredSheet.tsx` or equivalent renderer components.
- `src/ui/fields/` for label/control/help/error composition.
- Template and renderer tests under `tests/unit/templates/` and component tests.

**Acceptance criteria:**

- A Sheet Template Version has stable identity, version identity, structured field definitions, and an immutable lookup contract.
- A Table can select one Sheet Template and new Character Sheets use the approved available version without silently changing existing sheets.
- Existing Character Sheets continue to render from their recorded Sheet Template Version when a later registry version is added in a fixture/test.
- The renderer produces persistent labels, requiredness, help text/units where defined, validation messaging, and keyboard-accessible controls.
- Rendering is data-driven enough to support future templates while keeping this MVP to the seeded template(s).
- No formulas, rules automation, image fields, authoring UI, automatic migration, or opaque “render arbitrary HTML” escape hatch is introduced.

**Tests:**

- Registry tests for lookup, duplicate/version rejection, immutability, and stable version selection.
- Renderer tests for field order, labels, values, validation, keyboard semantics, and unknown/invalid field definitions.
- A compatibility test proving a sheet keeps using its recorded version after a newer version is available.

**Reviewer focus:**

- Is a Sheet Template clearly distinguished from a Character Sheet?
- Can a contributor inspect and review the structured definition without reverse-engineering UI code?
- Are all controls accessible and safe for long labels, zoom, and narrow layouts?
- Does the renderer keep the application shell consistent while allowing template-specific field grouping?

**True dependencies:** A. C for persisted version identity and structured data boundaries. B for shared UI/accessibility primitives.

### E. Implement Create Table and GM Link

**Scope:** Build the GM's first journey: create a Table with one Sheet Template, persist it, and present a usable GM Link at a stable application route. Keep setup minimal and avoid inventing unapproved fields.

**Likely files/modules:**

- `src/features/tables/` for create use case, form, validation, and result view.
- `src/features/access/` for link generation/resolution and copy affordance.
- `src/app/routes.tsx` route entries for Table creation and GM view.
- `tests/integration/tables/` and `tests/e2e/gm-create-table.spec.*`.

**Acceptance criteria:**

- From the stable application URL, a GM can create a Table with exactly one Sheet Template.
- Required inputs and validation are clear, entered values survive validation errors, and no account/sign-up flow appears.
- Creation persists the Table and GM Link in SQLite.
- The result identifies the Table, shows a readable/copyable GM Link, explains its trusted-GM purpose, and offers the next route to Players.
- Refreshing or opening the GM Link in a fresh browser context returns to the same Table.
- The normal application URL and link route are stable; no temporary preview URL is treated as a product link.

**Tests:**

- Domain/use-case integration tests for Table and GM Link creation.
- Browser journey test for create → result → copy/confirmation → reopen GM Link.
- Validation and keyboard tests for the create form and link result.

**Reviewer focus:**

- Does the flow use Table and Sheet Template correctly without inventing campaign/session/account concepts?
- Is the link result useful without promising recovery or rotation?
- Is copy confirmation accessible and non-blocking?
- Is the implementation small enough to leave Player Slots for the next slice?

**True dependencies:** B, C, and D.

### F. Add Player Slots and Player Links

**Scope:** From a GM Link, let the GM view the current Table's Player Slots, add a slot, and copy the resulting Player Link. Keep slot data as a position in a Table, not a person or account.

**Likely files/modules:**

- `src/features/player-slots/` for list, create form, empty state, and link result.
- `src/features/access/` additions for Player Link resolution.
- `src/app/routes.tsx` GM/Players routes.
- `tests/integration/player-slots/` and `tests/e2e/gm-player-slots.spec.*`.

**Acceptance criteria:**

- The GM can reach Players from a valid GM Link and sees the current Table context.
- The empty state explains Player Slots and gives one clear Add Player Slot action.
- The GM can create one or more Player Slots using only confirmed slot information and receives a copyable Player Link for each.
- A Player Link resolves to the correct Player Slot and never exposes an account/profile claim.
- Refreshing the Players route retains slots and links from SQLite.
- The list and result remain usable on narrow screens and with keyboard navigation.

**Tests:**

- Repository/use-case tests for slot ownership, uniqueness/lookup, and Player Link resolution.
- Browser tests for empty Players → add slot → copy link → return to list; add at least two slots.
- Accessibility tests for list semantics, link copy controls, and empty/loading/error states.

**Reviewer focus:**

- Is a Player Slot modeled as a durable position rather than a user identity?
- Are link actions clear without email invitations, rotation, or permissions matrices?
- Does the screen avoid collaborative presence and roster/social features?

**True dependencies:** E. B and C remain foundational; D's template selection need only be available through the Table created in E.

### G. Build Player welcome, My Sheets, and create Character Sheet

**Scope:** Implement the Player Link journey through the named-sheet creation boundary: welcome, My Sheets list/empty state, create form, mandatory Character Name, and persistence against the Table's selected Sheet Template Version.

**Likely files/modules:**

- `src/features/player/Welcome.tsx`, `src/features/player/MySheets.tsx`, and `src/features/player/CreateCharacterSheet.tsx` (or equivalent feature modules).
- `src/features/character-sheets/` creation use case and list view.
- `src/app/routes.tsx` Player Link routes.
- `tests/integration/character-sheets/` and `tests/e2e/player-create-sheet.spec.*`.

**Acceptance criteria:**

- A Player Link opens a concise welcome view identifying the Table and explaining that the slot can have multiple Character Sheets.
- My Sheets lists existing Character Sheets by Character Name and has a clear create action.
- The empty state and returning state preserve context and do not add search, deletion, favorites, sharing, or profiles.
- Character Name is visibly required and cannot be blank or whitespace-only; the error is adjacent, plain-language, and preserves input.
- Successful creation persists a Character Sheet for the Player Slot with the Table's selected immutable Sheet Template Version and returns to the sheet/editor entry point.
- A player can create at least two named Character Sheets from the same Player Link.

**Tests:**

- Domain/use-case tests for mandatory Character Name and Player Slot ownership.
- Browser tests for welcome → My Sheets empty → create valid sheet → list; create a second sheet; refresh and reopen.
- Keyboard and validation tests proving the blank-name path is perceivable and cannot create a record.

**Reviewer focus:**

- Is Character Name part of persisted structured data and not merely display metadata?
- Does the flow consistently say Character Sheet, Character Name, Player Link, Player Slot, Table, and Sheet Template?
- Does it avoid account, profile, image, formula, rules, and external-integration setup?

**True dependencies:** F and D. C provides durable sheet creation and version association.

### H. Add Character Sheet read/update/editor

**Scope:** Add the editor route and use cases for reading and updating a persisted Character Sheet through its recorded Sheet Template Version. Keep editing visible and structured; do not add autosave yet beyond a clear update seam.

**Likely files/modules:**

- `src/features/character-sheets/CharacterSheetPage.tsx`, editor state, and read/update use cases.
- `src/templates/` renderer integration and field validation adapters.
- `src/ui/status/` additions for a placeholder saved/unsaved state only if needed by the editor seam.
- `tests/integration/character-sheets/` and `tests/e2e/character-sheet-editor.spec.*`.

**Acceptance criteria:**

- A Player can open a Character Sheet from My Sheets and see its Character Name, Table context, and structured fields.
- Fields are rendered from the sheet's recorded Sheet Template Version, with labels/help/validation from the definition.
- A valid field update persists to SQLite and is visible after refresh and reopening from My Sheets.
- Invalid field input is explained without clearing the user's value; the sheet remains visible and editable.
- The editor provides a quiet route back to My Sheets without introducing a manual Save button as a competing product action.
- The implementation does not add formulas, images, conflict UI, presence, or template migration.

**Tests:**

- Read/update integration tests, including version-specific rendering and validation.
- Browser tests for open → edit structured field → navigate away → reopen and observe the persisted value.
- Accessibility tests for label association, field errors, focus, and reading order.

**Reviewer focus:**

- Is the editor a structured form rather than a free-form opaque document?
- Does the Character Sheet remain usable during updates and on narrow screens?
- Are read and update routes scoped to the Player Link/slot without claiming stronger identity?

**True dependencies:** G and D. C supplies update persistence.

### I. Add debounced autosave and relative last-saved feedback

**Scope:** Replace the editor's explicit update seam with debounced save-on-change behavior, visible pending/in-progress/success/failure states, exact saving copy, relative last-saved text, and deterministic one active browser and last-write-wins behavior.

**Likely files/modules:**

- `src/features/character-sheets/autosave.ts` or hook/state machine.
- `src/features/character-sheets/CharacterSheetPage.tsx` status placement and retry action.
- `src/ui/status/AutosaveStatus.tsx` and relative-time utility.
- `tests/unit/autosave/`, `tests/integration/character-sheets/`, and `tests/e2e/autosave.spec.*`.

**Acceptance criteria:**

- A burst of field edits is grouped behind a short documented debounce rather than one request per keystroke.
- While the latest change is pending or being saved, the UI shows a small inline loader and exactly `Saving your latest change…`.
- After a successful save, the sheet shows relative text in the prescribed style, such as `Last saved 10 minutes ago`, close to the document/header without covering fields or stealing focus.
- The status is informational and there is no competing Save button.
- If saving fails, the edited value remains visible, the UI distinguishes unsaved/error from saved, and a clear inline retry path is available. It must not claim a failed change was saved.
- Last-write-wins is deterministic for overlapping writes; no conflict dialog, merge UI, or multi-user presence is introduced.
- Refresh/reopen after a successful save shows the persisted value and a correct relative last-saved state.

**Tests:**

- Unit tests with fake timers for debounce grouping, cancellation/replacement, relative-time formatting, and status transitions.
- Integration tests for successful, failed, retried, and ordered overlapping writes.
- Browser tests that observe the exact saving text, then successful relative last-saved text, including after reload.
- Accessibility tests for polite status announcements, loader naming, focus retention, and error/retry semantics.

**Reviewer focus:**

- Is there one clear source of truth for save state, and can stale responses incorrectly replace newer values?
- Does the UI honestly distinguish pending/failed from saved?
- Is the exact prescribed copy preserved, and is the status visible without disrupting play or typing?
- Does the debounce reduce writes without making the application feel unresponsive?

**True dependencies:** H and C. The editor must expose a stable update seam before autosave is attached.

### J. Finish responsive and accessibility hardening

**Scope:** Exercise the complete local journey at narrow and wide viewports and harden semantic markup, keyboard operation, focus, status messaging, contrast, zoom/text enlargement, reduced motion, and recoverable/unavailable states. This is a focused quality slice, not a redesign or feature expansion.

**Likely files/modules:**

- Targeted fixes across `src/ui/`, `src/features/`, and style/token files.
- `tests/e2e/accessibility.spec.*`, `tests/e2e/responsive.spec.*`, and accessibility test configuration.
- Contributor-facing test/setup notes only if required to reproduce the checks.

**Acceptance criteria:**

- GM and Player journeys work without horizontal scrolling at supported narrow widths and remain comfortable on desktop/tablet.
- Keyboard-only navigation reaches every action, retains a visible focus indicator, follows logical order, and does not trap focus.
- Labels, requiredness, validation, copy confirmation, autosave changes, errors, and unavailable-link states are perceivable to assistive technology without interrupting typing.
- Text enlargement/high zoom, long Character Names/labels, landscape phone layouts, and reduced-motion preferences do not hide essential content or controls.
- Contrast and touch-target behavior meet the UI brief's accessibility intent; status is not conveyed by color or motion alone.
- The full end-to-end acceptance journeys pass from a clean local database.
- No deployment, Sheet from Beyond setup documentation, or other deferred feature is added during hardening.

**Tests:**

- Automated accessibility checks for all route families, supplemented by keyboard journey tests.
- Browser matrix for narrow/wide viewport, zoom/text scaling where automation permits, and reduced motion.
- Full local end-to-end suite from clean database through GM, Player, editor, persistence, and autosave journeys.

**Reviewer focus:**

- Are fixes systemic and reusable rather than one-off selectors?
- Do visual refinements preserve the calm, paper-like UI brief and field clarity?
- Does the test evidence cover the critical link-to-editor journey and failure states?
- Did this slice avoid absorbing future work?

**True dependencies:** I for the complete user journey; B–H provide the route and component surfaces being hardened.

## Local end-to-end acceptance journeys

These journeys are the release gate for the local MVP. Each should start with a clean temporary SQLite database and the documented stable local URL. Tests may use multiple browser contexts to represent links and the one active browser assumption; they must not use accounts or cloud services.

### 1. GM creates a Table and distributes access

1. Open the stable application URL.
2. Choose Create a Table and provide only the confirmed required setup values.
3. Confirm the Table uses exactly one Sheet Template and submit.
4. Verify a readable GM Link result, accessible copy confirmation, and a route to Players.
5. Reopen the GM Link in a fresh browser context and verify the same Table is present.
6. Open Players, verify the empty state, add at least two Player Slots, and copy each Player Link.
7. Refresh Players and verify both Player Slots and their links remain available.

### 2. Player creates multiple named Character Sheets

1. Open one Player Link in a fresh browser context.
2. Verify the Player welcome view names the Table and explains that the Player Slot can have multiple Character Sheets.
3. Continue to My Sheets and verify the empty state has one clear create action.
4. Attempt to create a Character Sheet with an empty and whitespace-only Character Name; verify no record is created and the adjacent error is perceivable.
5. Enter a valid Character Name and create the Character Sheet.
6. Verify the Character Sheet is associated with the Table's Sheet Template Version and is listed by Character Name.
7. Return to My Sheets, create a second named Character Sheet, and verify both are listed independently.

### 3. Player edits and finds durable data

1. Open a named Character Sheet from My Sheets.
2. Verify the Character Name, Table context, and structured fields are labeled and keyboard reachable.
3. Change a field and observe the inline `Saving your latest change…` state.
4. Wait for successful save and verify relative `Last saved … ago` text.
5. Navigate to My Sheets, reopen the sheet, and verify the changed value.
6. Stop and restart the local application without deleting SQLite data; reopen the link and verify the value remains.

### 4. Autosave failure and last-write-wins behavior

1. Exercise a controlled local save failure (test adapter or deterministic fixture, not a cloud outage).
2. Verify the edited value remains visible, the state does not claim it was saved, and inline retry succeeds without a modal or focus theft.
3. In an integration fixture, submit two ordered updates for one Character Sheet and verify the later accepted write is the visible persisted value.
4. Verify no conflict, merge, presence, or collaborative-editing UI appears.

### 5. Accessibility and responsive journey

1. Complete the GM and Player journeys with keyboard only.
2. Confirm the reading order, landmarks, labels, requiredness, validation, copy confirmation, autosave status, and retry are perceivable.
3. Repeat the player journey at a narrow mobile viewport and a wide desktop viewport.
4. Check long Character Names/labels, high zoom/text enlargement, landscape orientation, and reduced-motion preference.
5. Verify no essential control or status is hidden behind hover, horizontal scrolling, decorative treatment, or the virtual keyboard.

## Risks and mitigations

- **Unsettled setup details:** The README and UI brief leave some fields and labels for product validation. Keep the create forms minimal, record any implementation default in the PR, and do not invent a broader configuration model.
- **Access-link exposure:** Link possession is intentionally the MVP access mechanism. Use sufficiently opaque local values and neutral copy, but defer rotation, recovery, and account-grade access to future work rather than implying security guarantees.
- **SQLite concurrency and stale writes:** A local file is simple but still needs transaction and update-order tests. Keep writes narrow, make last-write-wins explicit, and avoid adding a false conflict model.
- **Template immutability drift:** A registry that mutates definitions would make old Character Sheets ambiguous. Store stable template/version identity, test duplicate/mutation rejection, and render by the sheet's recorded version.
- **Opaque sheet data:** Free-form JSON without a reviewed structure would undermine extraction and future evolution. Keep field definitions and value validation explicit, with a narrow structured data contract.
- **Autosave races and misleading feedback:** Debounce, retries, failures, and stale responses can lose or misreport edits. Centralize the autosave state machine, use fake-timer tests, preserve visible values, and announce only truthful status.
- **Accessibility regressions from paper styling:** Low-contrast rules, tiny labels, or decorative surfaces can make the form unusable. Treat semantic markup, focus, contrast, zoom, and reduced motion as acceptance criteria, not polish.
- **Route instability:** Generated or temporary URLs would break copied GM Links and Player Links. Keep ordinary deterministic application routes and test refresh/direct navigation explicitly.
- **Scope creep toward production:** Deployment, observability, Sheet from Beyond documentation, and integrations can look like natural next steps while obscuring the local gate. Keep them in `docs/future-work.md` and reject mixed-concern PRs.
- **Test flakiness:** Browser tests that depend on timing or a shared database will obscure regressions. Use isolated fixtures, deterministic clock/network adapters, stable selectors/accessible names, and a clean database per journey.
- **Local environment variance:** Different Node versions or database paths can make the MVP non-reproducible. Pin/document the supported runtime and make SQLite paths and scripts explicit in the bootstrap PR.

## Completion standard

The plan is complete when all ten slices can be reviewed independently, the local end-to-end journeys pass from a clean SQLite database, the application uses the canonical domain language, and the implementation still excludes every non-goal listed above. A passing local MVP is not evidence that deployment, production operations, or any deferred integration is complete.
