# SheetHarbour

SheetHarbour is an open-source, external web application for persistent tabletop RPG character sheets. It is initially intended to be used alongside Sheet from Beyond and Owlbear Rodeo. It is not a native Owlbear Rodeo extension in the MVP.

This README is the living product requirements document. It records the product boundary and the decisions from the initial investigation without prescribing an implementation, API, or deployment provider.

## Problem and vision

Tabletop groups often keep character sheets in browser-local state, in files that are difficult to share, or in tools that do not persist reliably when a player changes browser or computer. Groups need a simple way to open the right sheet, make ordinary character updates, and find those updates again later without creating accounts or adopting a full virtual tabletop rules system.

SheetHarbour should make a character sheet feel like a durable, shared tabletop artifact: easy for a GM to set up, easy for a player to use, and structured enough to evolve as sheet definitions improve. The first product should stay close to pen and paper rather than trying to automate the rules of an RPG.

## Product position and assumptions

- **External web application:** SheetHarbour is hosted separately from Owlbear Rodeo and is opened as a web URL.
- **Initial integration:** Sheet from Beyond can open a SheetHarbour URL from Owlbear Rodeo as a one-way handoff. SheetHarbour does not promise to synchronize changes back to Sheet from Beyond or Owlbear Rodeo.
- **Trusted groups:** The MVP is for trusted tabletop groups. Access links are intentionally simple, and the product does not yet attempt to provide account-grade identity, recovery, or access administration.
- **Open source:** The project should remain inspectable and self-hosting-friendly, even when the initial pilot uses hosted infrastructure.

## Domain language

The canonical terms and their meanings are defined here. In particular, use **Table** rather than “game container”, “campaign”, or “session” for the SheetHarbour container. A Table is not the same thing as an RPG or game system:

- An **RPG/game system** is the ruleset or game for which a sheet may be designed.
- A **Table** is one persistent SheetHarbour instance for a tabletop group and its play. It brings together a GM, Player Slots, and that group's Character Sheets.
- A Table uses exactly one **Sheet Template**. Different Tables may use the same Sheet Template.
- A **Player Slot** is a position within a Table, not an account or a statement about a person's identity.
- A **GM Link** opens the GM view of a Table. A **Player Link** opens the player view of a Player Slot; it is not itself a Character Sheet.

## Target users

### Game masters

A GM wants to establish a Table quickly, give each participant a simple link, and have enough visibility to manage the group's Player Slots without managing accounts.

### Players

A player wants to use a Player Link from any supported browser or computer, create one or more Character Sheets for that Table, and update those sheets without manually saving after every change.

### Contributors and maintainers

Contributors and maintainers define and evolve Sheet Templates. They need structured, versioned sheet definitions so that a fix or a new layout does not silently change the meaning of existing Character Sheets.

### Trust assumption

The MVP assumes that links are shared only with the intended tabletop group. Link possession is the practical access mechanism. This is suitable for an initial trusted-group pilot, but it is not a substitute for stronger identity and access controls.

## MVP goals

The MVP should provide:

1. **A durable Table and link flow.** A GM can create a Table, receive its GM Link, create Player Slots, and copy a Player Link for each slot.
2. **Player-owned sheets in a Table.** A player can use a Player Link to create multiple Character Sheets from the Table's Sheet Template and return to them later.
3. **Mandatory character identity.** Every new Character Sheet requires a Character Name before it can be created. A sheet with a missing Character Name is not a valid new sheet.
4. **Structured, versioned data.** Character Sheet fields are structured and extractable rather than an opaque browser-only form. Each sheet records the Sheet Template Version that defines it.
5. **Persistence across devices.** Character Sheets persist in the hosted application and can be opened from another browser or computer using the appropriate link.
6. **Low-friction editing.** Changes save automatically after a debounce; a player does not need to find or press a Save button for ordinary edits.
7. **Clear save feedback.** The Character Sheet shows a simple inline loader while a save is pending or in progress and shows relative last-saved text such as “just now” or “3 minutes ago”.
8. **A deliberately small pilot.** The product can be operated as a low-operations external service for trusted groups without requiring a large infrastructure footprint.

The MVP assumes one active browser at a time for a sheet. If writes overlap, the expected behavior is last-write-wins; conflict resolution and collaborative editing are not MVP requirements.

## MVP non-goals

The MVP does not promise:

- user accounts, sign-in, or account-based identity;
- a native Owlbear Rodeo extension, room binding, or two-way synchronization;
- formulas, rules automation, or a rules engine;
- images or an image/media management system;
- automated deletion, expiration, or cleanup of Tables or sheets;
- backups as a product feature;
- reminder email or other email workflows;
- link rotation, link recovery, or a full link-revocation workflow;
- strong access control beyond the trusted-group access-link assumption;
- simultaneous multi-browser editing or conflict resolution.

These exclusions keep the pilot focused on persistent structured sheets. They do not prevent later manual data-management capabilities, but no such behavior should be implied until it is specified.

## Core product behavior

### Table and Player Slot model

A Table represents one RPG/game context for one tabletop group. Creating a Table does not create an account and does not mean that the Table is an RPG/game system. The GM receives a GM Link for that Table.

From the GM view, the GM creates Player Slots and copies the resulting Player Links to the intended participants. A Player Slot is the durable place for that participant's sheets within the Table; it is not a user record or account identity.

Different Tables can select the same Sheet Template. A Table selects one template for its sheets rather than mixing unrelated templates inside the same Table.

### Sheet Template and versioning

A Sheet Template is a contributor-provided definition of a kind of Character Sheet: its structured fields and the presentation needed to edit them. It defines the shape of a sheet; it is not a Character Sheet itself.

Sheet Template Versions are immutable revisions of a Sheet Template. The requirements are:

- a Character Sheet records the exact Sheet Template Version that defines it;
- changing a template creates a new version rather than silently changing the old version;
- a new Table uses one Sheet Template and should have a clear rule for which available version is used for new sheets;
- existing Character Sheets remain interpretable against their recorded version;
- when a later version is compatible, the product may eventually migrate a sheet or offer a replacement, but the MVP does not assume automatic migration.

Template authoring, validation, publishing, and migration tooling are intentionally left implementation-agnostic and remain open questions below.

### Character Sheet and Character Name

A Character Sheet is an individual sheet belonging to a Player Slot in a Table. A Player Link can lead to multiple Character Sheets, so the link is not limited to one character.

Creating a new sheet requires a Character Name. The creation flow must make that requirement apparent and must not create an unnamed sheet. The Character Name is part of the sheet's persisted structured data. Other fields come from the selected Sheet Template Version and can be edited as ordinary sheet data.

### Autosave and last-saved feedback

Editing is save-on-change rather than button-driven:

1. The player changes one or more fields.
2. SheetHarbour waits for a short debounce so a burst of edits can be saved together.
3. An inline loader communicates that the change is pending or being saved.
4. After a successful save, the Character Sheet displays relative last-saved text inside the sheet.

The MVP does not define multi-device conflict handling: one active browser is assumed and overlapping writes use last-write-wins. The detailed retry and error presentation for a failed save is an open product question, not an excuse to claim that a change was saved when it was not.

## User flows

### GM flow

1. The GM opens SheetHarbour and creates a Table for one tabletop RPG context.
2. SheetHarbour presents the GM Link for that Table.
3. The GM creates one or more Player Slots.
4. The GM copies and shares each Player Link with the intended participant.
5. The GM returns through the GM Link to manage the Table's slots and the group's sheet access.

The flow must not require an account in the MVP.

### Player flow

1. The player opens a Player Link, whether directly or from a handoff initiated in Owlbear Rodeo through Sheet from Beyond.
2. The player sees the sheets associated with that Player Slot and can start a new Character Sheet.
3. The player chooses the Table's available Sheet Template flow and supplies the mandatory Character Name.
4. SheetHarbour creates the named sheet against a Sheet Template Version.
5. The player edits structured fields. Autosave and last-saved feedback make persistence visible without a manual Save action.
6. The player later reopens the Player Link from another browser or computer and returns to the persisted sheets.

### Sheet from Beyond / Owlbear Rodeo handoff

The initial integration direction is deliberately one-way: Sheet from Beyond, launched from Owlbear Rodeo, opens a SheetHarbour URL. That handoff can help a group get to its external sheet, but SheetHarbour is not promised to know about Owlbear rooms, receive room state, or send sheet changes back. A native Owlbear extension and room binding are later roadmap items, not MVP behavior.

## Architecture, hosting, and pilot cost direction

SheetHarbour is an external persistent web application, not a browser-local-only feature. The initial investigation favors a small, managed hosting setup with a persistent data store and as few operational services as possible. The product should be able to start as one modest web application backed by durable hosted storage; it does not need a dedicated multi-service platform or global-scale operations for the pilot.

The pilot cost direction is low and usage-driven rather than infrastructure-heavy:

- prefer managed services with low operational overhead and a small baseline cost;
- avoid paying for services that the MVP does not use, such as email delivery or image storage;
- validate the cost model around the investigated small-pilot range (approximately 100 Tables per month) and understand the path toward approximately 1,000 Tables per month;
- treat exact providers, deployment topology, quotas, pricing, and scaling thresholds as implementation decisions, not product guarantees.

The architecture must preserve structured sheet data and template-version identity across browsers and computers. Hosting durability or provider-level operational features should not be described as an MVP user-facing backup promise.

## Later roadmap

After the MVP has demonstrated that trusted groups can create and use durable sheets, likely follow-on work includes:

1. Native Owlbear Rodeo integration, room binding, and a clearer two-way integration model.
2. Deliberate deletion and lifecycle controls, backups, and reminder email.
3. Link rotation, recovery, and stronger access control or account-based identity.
4. Formulas, rules assistance, and other optional automation for supported RPG systems.
5. Images and richer media fields.
6. More capable multi-device synchronization and conflict handling.

Roadmap items require their own requirements and must not be inferred from the MVP's simple link model.

## Success criteria

The MVP is successful when a trusted tabletop group can demonstrate that:

- a GM creates a Table and obtains a GM Link without an account;
- the GM creates Player Slots and shares Player Links;
- a player uses a Player Link to create more than one named Character Sheet from the Table's template;
- an unnamed new sheet cannot be created;
- edits persist after leaving and returning from another browser or computer;
- the Character Sheet makes autosave activity and relative last-saved time understandable;
- each sheet remains associated with the Sheet Template Version that defines its fields;
- Sheet from Beyond can open the external SheetHarbour destination without implying synchronization back to Owlbear Rodeo; and
- the pilot remains simple enough to operate at low cost for trusted groups.

## Open questions

The following decisions are intentionally not settled by this PRD:

- What exact access-link format, entropy, display, sharing warning, and revocation behavior should be used?
- How can a GM recover access if a GM Link is lost, given the no-account MVP?
- How are Player Slots labeled, reordered, or handed to a different participant?
- What is the authoring and publishing workflow for contributor-provided Sheet Templates?
- How are available Sheet Template Versions selected for a new Table and for a new Character Sheet?
- When is a template change compatible enough to migrate existing sheets, and when should it offer a replacement instead?
- What validation rules apply to Character Name beyond the requirement that it be present?
- What should players see when autosave fails, and how should unsaved edits be retried without overstating persistence?
- What manual deletion behavior, if any, belongs in the first usable release while automated cleanup remains out of scope?
- Which hosting provider and persistent storage arrangement best fit the pilot budget and the approximately 100-to-1,000-Tables-per-month growth path?
- Which stronger access-control and identity features should precede a move beyond trusted tabletop groups?
