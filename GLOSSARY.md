# SheetHarbour Domain Language

SheetHarbour is an external persistent character-sheet service, initially for trusted tabletop RPG groups using Sheet from Beyond. This glossary names the product's domain concepts without prescribing how they are implemented.

## Group and participation

**Table**:
The persistent SheetHarbour container for one tabletop RPG group. A Table brings together its GM, Player Slots, and the Character Sheets created for that group.
_Avoid_: Game, Campaign, Session

**Player Slot**:
A participant position inside a Table. It is the place to which a player's Character Sheets belong, not a person or an account identity.
_Avoid_: User, Account

## Sheets and definitions

**Character Sheet**:
An individual player-owned sheet for a character in a Table. A Player Slot may own one or more Character Sheets, and each Character Sheet is defined by a Sheet Template Version.
_Avoid_: Model

**Sheet Template**:
A contributor-provided definition of the fields and layout for a kind of Character Sheet. It describes the shape of sheets without being a Character Sheet itself.
_Avoid_: Template

**Sheet Template Version**:
An immutable revision of a Sheet Template. A Character Sheet uses one version as its definition; a later revision is a new version rather than a change to the old one.

## Access

**GM Link**:
The user-facing access link for the GM of a Table. It opens the GM's view of that Table.
_Avoid_: URL

**Player Link**:
The user-facing access link for a Player Slot in a Table. It opens that slot's player view and is not the Character Sheet itself.
_Avoid_: URL
