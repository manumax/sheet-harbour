# SheetHarbour — MVP UI/UX design brief

**Audience:** Product designer establishing the visual design and design system for the MVP
**Product shape:** Calm, paper-like external web application for trusted tabletop RPG groups
**Scope:** The first usable experience for GMs and players, including the one-way launch from Sheet from Beyond

## 1. Product context

SheetHarbour keeps tabletop RPG character sheets available through durable web links rather than browser-local state. A GM creates a **Table**, receives a GM Link, creates Player Slots, and copies Player Links for the group. A player opens a Player Link and can create multiple Character Sheets. A Character Name is required before a sheet can be created.

A Table is the canonical game context: one Table represents one RPG/game context and uses one Sheet Template. Different Tables may use the same template. Character fields are structured and versioned, but the MVP should feel as straightforward as writing on a sheet of paper.

The initial use case is Sheet from Beyond. The GM configures a stable HTTPS SheetHarbour character URL in Owlbear character/token metadata. Players open that URL one-way through Sheet from Beyond. SheetHarbour is not a handoff workflow or a room-management surface.

### Product principles

1. **Calm over clever.** Reduce ceremony and visual noise; let the sheet remain the focus.
2. **Paper familiarity, web reliability.** Borrow the warmth and legibility of a well-kept paper document without pretending the interface is a scanned page.
3. **One clear next step.** Each screen should make the primary action and current context obvious.
4. **Trust without account friction.** Links are intended for trusted groups. Do not create login, registration, or security-dashboard experiences in the MVP.
5. **Persistent, not precious.** Editing should feel safe because changes save automatically, while never hiding the current sheet behind a save flow.
6. **Respect the game table.** The UI should support quick glances, interruptions, and returning to a sheet during play.
7. **Accessible by default.** Paper-like styling must not compromise contrast, focus, readable type, or keyboard operation.

## 2. Users and assumptions

### Primary users

- **GM:** Creates and names a Table, gets the GM Link, creates Player Slots, and copies Player Links to the group. May manage several players in one Table.
- **Player:** Opens a Player Link, creates and names one or more Character Sheets, and edits those sheets during and between sessions.
- **Character sheet reader:** A player or GM quickly checking values while playing. The experience must work for a glance as well as a long editing session.

### Trusted-group assumptions

- A GM shares the GM Link and Player Links deliberately with a small, trusted group.
- A Player Link identifies an access lane without requiring an account. Avoid implying account ownership, profiles, or identity verification.
- The MVP assumes one active browser per player and uses last-write-wins. Do not design conflict dialogs, merge views, presence indicators, or collaborative cursors.
- A link may be unavailable or unusable; explain that plainly without exposing technical details or inventing recovery/account flows.

## 3. Terminology

Use these terms consistently. The [root GLOSSARY.md](../GLOSSARY.md) is the canonical terminology reference.

| Term | Meaning and usage |
| --- | --- |
| **Table** | One RPG/game context. Use this instead of “game,” “campaign,” “room,” or “container” in user-facing copy. |
| **Sheet Template** | The structured, versioned definition used by a Table’s Character Sheets. |
| **GM** | The person who creates and manages a Table. Prefer “GM” over “game master” in compact UI labels. |
| **GM Link** | The durable link the GM uses to manage a Table. Treat it as private to the GM. |
| **Player Slot** | An access slot created by the GM for a player. |
| **Player Link** | The durable link copied by the GM and used by a player to access that Player Slot. |
| **Character Sheet** | One player-created sheet within a Table. A Player Link can be used to create multiple sheets. |
| **Character Name** | The required name entered when creating a Character Sheet. |
| **Sheet from Beyond** | The initial one-way entry context. Keep this as a product name, not a generic synonym for the sheet. |

## 4. Visual direction to explore

Explore a visual language that feels like a calm, well-made field notebook or tabletop reference folio:

- warm, quiet surfaces; soft off-white paper rather than stark white;
- generous margins and a clear reading column;
- restrained ink-like text and accents, with one reassuring action color;
- subtle rules, registration marks, or paper grain only where they improve orientation;
- considered grouping and hierarchy instead of dense card dashboards;
- a little character and craft, but no faux-aged parchment, fantasy ornament, or game UI clichés;
- a responsive surface that remains a practical web form, not a literal image of a sheet.

The design should remain usable in bright rooms, dim play spaces, and on an ordinary phone. Decorative treatment must never reduce contrast, make fields look disabled, or compete with editable content.

## 5. Design-system goals and foundations

### Goals

- Establish a small, coherent system that can support management screens and varied sheet templates.
- Make the distinction between navigation, editable fields, status, and destructive/unavailable messaging unmistakable.
- Define reusable states and responsive behavior before polishing individual screens.
- Keep the system template-friendly: the shell should provide a consistent experience while the field layout can vary by Sheet Template.
- Make tokens and components understandable enough for future contributors to extend without creating a new visual language.

### Token categories

Provide named design tokens (not one-off screen values) for:

- **Color:** paper/surface layers, ink/text, muted text, rules/borders, primary action, focus, success/saved, warning, error, unavailable, and disabled states.
- **Typography:** family, size, weight, line height, letter spacing, and text styles for page titles, section headings, field labels, body, helper text, status, and button labels.
- **Spacing:** a compact base scale for page insets, section gaps, field gaps, control padding, and form rhythm.
- **Shape:** corner treatments, field radius, button radius, and any document/frame treatment.
- **Elevation:** surface levels for the page, document/sheet, sticky status or action areas, and transient feedback.
- **Borders and rules:** default, strong, focus, error, and disabled treatments, including rule thickness and style.
- **Motion:** short, quiet transitions for loading and feedback; no distracting animation. Respect reduced-motion preferences.
- **Iconography:** icon size, stroke/fill approach, alignment, and accessible naming.
- **Breakpoints and layout:** content max width, document insets, column changes, and minimum usable control width.

### Typography

Prioritize a highly legible sans-serif or humanist grotesk for controls and utility text. A restrained editorial or serif companion may be explored for page titles or document headings, but it must not reduce scanability. Define a small type scale with comfortable line height, clear label-to-input relationships, and a reliable mobile size. Avoid all-caps body copy and avoid type that looks handwritten or ornamental.

### Color

Start from a warm neutral paper and ink palette. Use color sparingly:

- ink should carry primary hierarchy;
- muted ink should remain readable, not become placeholder-gray;
- the primary action color should be distinctive and work on both light surfaces and focus states;
- success, warning, and error should have text or icon support, never color alone;
- focus must be visibly stronger than the paper rule around it;
- test all combinations for WCAG AA contrast, including disabled-looking but readable helper text.

### Spacing and layout

Use a consistent spacing scale with generous outer margins and a tighter but breathable field rhythm. Keep the main content column comfortable for reading and editing. Management screens may use a wider layout for lists, but avoid dashboard density. Establish a predictable relationship between page heading, explanatory text, primary action, and content surface.

### Elevation and borders

Prefer separation through whitespace, tonal paper layers, and thin rules. Use only a few quiet elevation levels; avoid heavy shadows, floating-card stacks, and glossy effects. A document surface may sit slightly above the page background, but the boundary must remain clear in low light and at high zoom. Define border behavior for normal, hover, focus, error, unavailable, and disabled states.

### Iconography

Use a small, consistent set of simple icons with text labels wherever an action is important. Favor familiar symbols for copy, add, back, external/open, warning, and saving. Do not use icons as the sole label for primary actions. Decorative marks should be optional and hidden from assistive technology.

### Paper/document treatment

Treat “paper” as a hierarchy and material cue, not a texture requirement. Explore a subtle paper tone, a document frame, gentle rule lines, and restrained grain at most. Keep editable fields crisp and unmistakable. Never use a background image, texture, or skewed perspective that makes text hard to select, print, zoom, or read.

## 6. Component inventory

The designer should define reusable components and their states, including:

- application/page shell and document surface;
- page header with context, title, supporting copy, and primary action;
- GM/Player context marker and lightweight Sheet from Beyond context marker;
- buttons: primary, secondary, quiet, destructive/unavailable where needed, with loading and disabled states;
- text input, textarea if a template needs it, and structured sheet field controls;
- required-field label, helper text, validation message, and character-name field;
- link result panel with copy action and copied confirmation;
- Player Slot row/card, status, and overflow/secondary action treatment if required;
- list/table layout that collapses cleanly on small screens;
- inline status line for autosave, including saving, saved, and save-error variants;
- loader/skeleton for page and sheet loading;
- empty-state panel with explanation and one clear action;
- inline alert for recoverable errors and a plain unavailable state;
- divider/rule, breadcrumb/back affordance where needed, and pagination only if the design discovers a real need;
- tooltips only for non-essential supplementary explanation, never for required instructions.

Document variants, content limits, focus behavior, responsive changes, and accessibility names for each component.

## 7. Screen-by-screen requirements

The screens below describe the MVP surface. Show the user where they are and what the current Table or Player Link represents without adding account or room concepts.

### Create Table

**Purpose:** Let a GM establish one Table and its Sheet Template with minimal setup.

- Use a clear title such as “Create a Table” and a short explanation of what a Table represents.
- Present only the confirmed setup choices and required information. The designer should not invent campaign-management fields.
- Make the selected Sheet Template and its relationship to this Table understandable; the Table uses one template.
- Primary action creates the Table. Provide a clear route back or cancellation only if the surrounding flow needs it.
- Show required fields before submission and preserve entered values after validation errors.
- Avoid sign-up, email capture, room binding, Owlbear permissions, or integration setup here.

### GM Link result

**Purpose:** Confirm that the Table exists and give the GM an immediately usable GM Link.

- Make the success state unmistakable without celebratory noise.
- Show the Table context and the GM Link in a readable, copyable presentation.
- Provide an obvious “Copy GM Link” action and a clear, non-blocking copied confirmation.
- Explain briefly that the GM Link is for the GM and should be shared carefully.
- Provide the next useful action: continue to Players or create the first Player Slot. Keep the link available after continuing where practical.
- Design the state for returning to the link later; do not imply an account or one-time-only recovery mechanism.

### Players

**Purpose:** Let the GM see and manage Player Slots for the current Table.

- Keep the Table name/context prominent and distinguish the GM area from player-facing sheet content.
- Show each Player Slot with its useful human-readable label and a copyable Player Link action.
- Provide a clear “Add Player Slot” action.
- Include a calm empty state when no slots exist: explain why a slot is useful and point to adding one.
- Do not invent roster profiles, online presence, chat, permissions matrices, or collaborative editing indicators.
- If a list action fails, retain the surrounding context and offer a local retry rather than sending the GM to a generic error page.

### Add Player Slot

**Purpose:** Let the GM create one access slot and receive its Player Link.

- Explain that the resulting Player Link is what the GM shares with one trusted player.
- Ask only for the confirmed slot information; if a slot name is optional or required, make that a design validation decision rather than assuming a profile.
- After creation, show the Player Link with the same copy affordance and confirmation pattern as the GM Link result.
- Make the route back to Players obvious.
- Avoid email invitations, automatic reminders, account creation, link rotation, and permission configuration.

### Player welcome

**Purpose:** Orient a player arriving through a Player Link before they choose or create a sheet.

- Identify the Table in a reassuring, concise way without exposing more information than the trusted link implies.
- Explain that this Player Link can be used for multiple Character Sheets.
- Provide one primary action to create a Character Sheet and a secondary route to view existing sheets when applicable.
- Keep the first view lightweight; do not make the player configure an Owlbear room or connect an external account.
- Include an empty variant for a new Player Link and a returning variant when sheets already exist.

### My Sheets

**Purpose:** Let a player choose an existing Character Sheet or start another one.

- Use a clear title such as “My Sheets” within the current Table context.
- Show existing sheets as readable, scannable items identified by Character Name and any confirmed secondary summary only.
- Provide a prominent “Create Character Sheet” action; creating another sheet must remain possible.
- Empty state: explain that no sheets have been created yet and make creation the single clear next step.
- Loading should preserve the page context; do not show a blank shell while the list is being retrieved.
- Do not add search, filters, favorites, sharing controls, or deletion flows unless a later product decision explicitly adds them.

### Create sheet / Character Name

**Purpose:** Start a Character Sheet with the required identity field and the selected Sheet Template.

- Make **Character Name** visibly required and explain why it is needed to identify the sheet in My Sheets.
- Do not allow creation with a blank or whitespace-only Character Name. Show validation next to the field, in plain language, while preserving the entered value.
- Show which Table and Sheet Template the sheet belongs to without turning this into a configuration wizard.
- The primary action should clearly create the sheet and move into the editor. The action remains unavailable only when the required input is invalid, and the reason must be perceivable.
- Keep the form focused: no account, image upload, formula setup, rules automation, or optional profile data in this MVP.

### Character Sheet editor

**Purpose:** Provide a calm, durable editing surface for structured Character Sheet fields.

- Keep Character Name and the Table context discoverable while editing.
- Use the Sheet Template’s field grouping and order, with clear labels, units/help text, and input affordances. The visual system should accommodate different structured templates without changing the application shell.
- Keep the sheet visible and editable while saving. Never replace the sheet with a modal save dialog or block editing during a save.
- Make field focus, validation, and changed-value feedback clear without turning the document into a dashboard.
- Do not add formulas, rules automation, images, synchronization indicators, conflict/merge UI, or multi-user presence UI.
- Provide a quiet path back to My Sheets or the Player context without losing the current editing surface unexpectedly.

#### Exact autosave presentation

Autosave happens after a debounce. The sheet remains visible and editable throughout.

- While the latest change is being saved, show a **small inline loader** beside the save status and the exact text: `Saving your latest change…`
- After a successful save, replace that state with a quiet timestamp such as: `Last saved 10 minutes ago`
- Keep the status close to the document/header or another consistently visible place; it must not cover fields or steal focus.
- The status is informational, not a save button. Do not add a competing “Save” action.
- If saving fails, keep the edited value visible, distinguish the error from the saved state, and provide a clear retry path without a modal. The designer should propose concise error copy and its state treatment for validation.
- Do not show conflict dialogs or merge UI. The MVP assumes one active browser per player and last-write-wins.

### Unavailable link

**Purpose:** Give a person a useful, humane response when a GM Link or Player Link cannot be used.

- Use a dedicated, plain-language state that says the link is unavailable without exposing implementation or security details.
- Keep the visual language consistent with the application, but make the unavailable condition immediately distinguishable from a normal empty state.
- Offer only appropriate next steps, such as checking the link or contacting the GM. Do not promise account recovery, automatic renewal, deletion recovery, or link rotation.
- Do not display a broken form, a fake sheet, or a generic technical error code.
- Ensure the state works for both direct browser visits and entry through Sheet from Beyond.

### Simple Sheet from Beyond context

**Purpose:** Make the one-way source of a character URL understandable without creating an integration workflow.

- Treat Sheet from Beyond as a lightweight context label or brief supporting note near the Character Sheet entry/editor, not as a handoff dialog.
- Acknowledge that the GM configured a stable HTTPS SheetHarbour character URL on Owlbear character/token metadata and the player opened it through Sheet from Beyond.
- The experience should transition directly to the relevant SheetHarbour page. There is no native Owlbear extension, room binding, synchronization, or return/handoff setup to design.
- Keep the context informational and dismissible only if it would otherwise persistently occupy editing space. Do not require a player to confirm, authorize, or connect anything.
- Design the direct URL experience first; the Sheet from Beyond context must be a small enhancement, not a separate product surface.

## 8. State requirements

Define states as deliberate component variants and include them in the design file and prototype.

- **Loading:** Use a small inline loader for short operations and a calm skeleton or paper placeholder for page/sheet loading. Preserve page context and avoid a blank white screen. Never use a spinner without nearby meaning.
- **Empty:** Explain what is absent, why it matters, and provide one clear next action. Empty is not an error and should not look disabled.
- **Validation error:** Place the message next to the relevant field, identify how to fix it, preserve input, and use text plus a visual cue rather than color alone.
- **Recoverable error:** Use an inline alert or status treatment, keep useful content visible, and offer retry where it can succeed. Avoid throwing the entire flow away.
- **Autosave error:** Preserve the user’s latest visible value, distinguish “not saved” from “last saved,” and provide a quiet retry treatment without blocking the sheet.
- **Unavailable:** Explain that the link or requested surface cannot be used, keep technical details out of user copy, and provide only realistic next steps.
- **Copied confirmation:** Confirm link copy near the action without a toast that disappears before it can be understood; keep it accessible to assistive technology.

## 9. Responsive behavior

- Design mobile first for the player’s direct-link journey, while supporting a comfortable desktop/tablet editing experience.
- Keep the primary action and current context visible without requiring horizontal scrolling.
- On narrow screens, collapse management lists and multi-column sheet groups into a readable single flow while preserving the template’s logical order and labels.
- Keep fields large enough for touch, with clear spacing between adjacent controls. Do not rely on hover to reveal essential actions.
- Let the document surface approach the viewport edges on small screens when that improves usable width; retain enough inset for readable text and focus indicators.
- Decide deliberately whether the autosave status is in normal flow or gently sticky on mobile; it must remain visible without covering the active field or keyboard.
- At larger widths, use a comfortable maximum reading/editing width and optional supporting rails only when they clarify context. Do not fill space with decorative panels.
- Test zoom, text enlargement, landscape phone use, and long Character Names/labels. Content must reflow rather than truncate essential information.

## 10. Keyboard and accessibility requirements

- Use semantic headings and landmarks with a predictable reading and tab order.
- Every field has a persistent, programmatically associated label; requiredness and validation are announced, not conveyed by color or placeholder text alone.
- Provide a strong, always-visible keyboard focus treatment that works against paper surfaces and rules.
- All actions, including copy link, retry, navigation, and sheet editing, are keyboard reachable and have clear accessible names.
- Do not trap focus in transient feedback. Announce copied confirmation, autosave state changes, and important errors politely without interrupting active typing.
- Maintain WCAG AA contrast for text, controls, focus, and status messaging. Do not make muted paper treatments too faint.
- Support reduced motion and avoid animation that conveys essential information only through movement.
- Ensure touch targets are comfortably usable and that controls remain usable with browser zoom and assistive technology.
- Error messages identify the field and correction; do not clear user-entered values after an error.
- Test the complete link-to-editor journey with keyboard-only navigation and a screen reader, plus high zoom and narrow viewport.

## 11. Copy and voice

The voice is **calm, plain, warm, and capable**. It should sound like a helpful table-side tool, not a game announcer or security console.

- Prefer concrete verbs: “Create a Table,” “Add Player Slot,” “Copy Player Link,” “Create Character Sheet.”
- Explain unfamiliar terms briefly the first time, especially Table, Player Slot, and Player Link.
- Keep instructions short and put the useful fact before the reassurance.
- Use “Character Sheet” and “Character Name” consistently; do not alternate with “character,” “profile,” or “document” when referring to the product objects.
- Avoid fear-based link language, technical error codes, marketing claims, and unnecessary exclamation marks.
- Do not imply that a player has an account, that a Table is an Owlbear room, or that changes are real-time synchronized.
- Use sentence case for headings and controls. Write timestamps in a human-readable way, including the prescribed `Last saved 10 minutes ago` pattern.
- The exact saving state is `Saving your latest change…` with a small inline loader. Keep any future failure copy equally direct and non-blaming.

## 12. MVP non-goals

Do not design or imply the following in this handoff:

- formulas, rules automation, or a rules engine;
- images, avatars, or image upload;
- accounts, login, registration, profiles, or password recovery;
- native Owlbear extension behavior or a handoff dialog;
- Owlbear room binding, two-way synchronization, or live presence;
- conflict resolution, merge UI, or multi-browser collaboration;
- link rotation, invitation email, reminder email, or email capture;
- deletion automation, backup, restore, or archival surfaces;
- speculative sharing permissions, roster/chat/social features, analytics, or notifications;
- implementation details such as API schemas, database models, storage architecture, or persistence internals.

## 13. Expected design deliverables

Please provide:

1. A short visual-direction exploration showing two or three viable paper/document approaches, with rationale and accessibility checks.
2. A foundational design-system page covering the token categories above, typography, color roles, spacing, borders, elevation, iconography, motion, and responsive rules.
3. A reusable component inventory with normal, focus, hover, disabled, loading, validation, saved, saving, error, empty, and unavailable variants where relevant.
4. High-fidelity responsive designs for every screen in Section 7, including the new/returning and success/error/empty states that apply.
5. A clickable prototype for the GM journey (Create Table → GM Link result → Players → Add Player Slot) and player journey (Player welcome → My Sheets → create sheet → Character Sheet editor), including direct-link and Sheet from Beyond context.
6. Content annotations for labels, helper text, validation, autosave, unavailable-link, and error states, preserving the exact autosave strings in this brief.
7. Accessibility and responsive annotations, including keyboard order, focus behavior, contrast intent, reduced motion, text expansion, and narrow-screen behavior.
8. A brief handoff note identifying reusable patterns, open decisions, and any visual treatment that is intentionally deferred from the MVP.

## 14. Decisions to validate, not invent

Please bring these questions back to the product owner rather than resolving them silently in the visual design:

- Which fields and labels are required when creating a Table, and how is the Sheet Template presented or selected?
- Is a Player Slot label required, optional, or generated, and what should the GM see before a player is associated with it?
- What exact confirmation and next action should follow creation/copying of a GM Link or Player Link?
- Which sheet fields, sections, units, and template-specific help belong in the first Character Sheet editor?
- What is the approved wording and realistic next step for an unavailable link, including whether the person should contact the GM?
- Where should the autosave status live on desktop and mobile, and what retry treatment is acceptable without adding a Save button?
- Which paper/document treatment best balances personality, print-like familiarity, contrast, zoom, and performance?
- Are any future-facing list actions (rename, delete, reorder, search) intentionally excluded from the first design, or should they be reserved as clearly marked follow-ups?
