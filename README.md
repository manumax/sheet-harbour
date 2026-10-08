# SheetHarbour

SheetHarbour is an open-source web application and Owlbear Rodeo extension for persistent tabletop RPG character sheets.

## Idea

A game master creates a game container for a supported roleplaying game and receives a durable GM URL. The GM can add players and retrieve each player's durable URL. Players use their URL with the Owlbear Rodeo extension to access their character sheets from different browsers or computers without relying on browser-local storage.

A player can create, update, and delete one or more character sheets for the game. Changes are persisted to the web application automatically, using debounced save-on-change writes so that players do not need to remember to click Save and frequent edits are batched.

The first version aims to stay as simple as pen and paper:

- no account login;
- no game automation, formulas, or special rules engine;
- no images initially;
- contributor-provided character-sheet templates;
- structured, extractable fields rather than opaque browser-only form state;
- sheet model and version identifiers stored with character data;
- compatible sheet fixes and new versions able to migrate existing data or offer a replacement;
- persistent web storage instead of browser memory;
- automatic deletion of inactive game containers after an initial 30-day period;
- optional email reminders seven days before deletion when the GM supplied an email address.

The GM and player URLs are currently envisioned as access links. Their security model, revocation behavior, sharing controls, and recovery options will be designed before implementation.

## Investigation

The initial investigation will compare:

1. existing Owlbear Rodeo extensions and hosted character-sheet services;
2. architecture and data-model options for versioned structured sheets;
3. hosting, storage, email, cleanup, and scaling costs for a small pilot and approximately 100 or 1,000 game sessions per month.

The product name, terminology for the game container, sheet-template format, and deployment choices are provisional and open to review.
