# Tabletop Simulator Editor — roadmap

Ideas agreed on 3 Nov 2026 for later versions, in the order they are worth doing. Nothing here is
started; details (syntax in particular) are decided when the work begins. Reference for what other
editors offer: rolandostar's VSCode extension (tabletopsimulator-lua-vscode) — its require/Include
resolution, Console++ and GUID finder were the features looked at; its autocompletion, XML completion,
asset downloader and debugging guide were judged not worth porting.

## 1. Include — 1.1.0

The specification is `INCLUDE.md` (same folder); this item only names it.

Split a script over several files locally while TTS still receives one script per object.

- A directive on its own line pulls in a file at push time. Syntax to be decided when implemented
  (`#include "framework/getPos.lua"` or `require("framework.getPos")` or both); extensionless paths,
  resolved against the script folder, then configured include folders (setting), nesting allowed.
- The pushed text wraps every included block in marker comments (start/end with the file name), so
  the structure stays recognisable in TTS and in the history snapshots.
- Get collapses marked blocks back to their directives and writes the included files only if they are
  missing locally — a Get never destroys the split; an edit made in TTS inside an included block is
  reported, not silently merged.
- Line numbers in TTS errors refer to the pushed (merged) text. Two ways to live with that, both
  wanted: (a) **jump to line**: a Lua error or a line reference in the output panel is clickable and
  the editor opens the right local file at the right line (Sublime's result navigation; the package
  keeps the line map of the last push per script); (b) **merge view**: a command that shows the full
  merged text of the open script read-only (what TTS runs), so a line number can simply be looked up.
- Execute Selection must still work from inside an included file (it runs in Global unless the file
  belongs to an object script).

## 2. Finder helpers (small, via Execute / custom messages)

Not GUID completion as in VSCode — with the framework GUIDs matter little — but:

- **Snap point numbers**: a command that labels the snap points of the selected/active object in the
  game (3D text with the index), replacing the helper function currently kept in the mod for that.
- **Show me**: highlight in the game the object(s) a selected expression resolves to (a `getPos` call,
  a `getObj` selection) — runs the selection through Execute and asks the mod to highlight the result.
  Needs a small Lua helper on the mod side; the package sends, the mod highlights.

## 3. Image URL replacement in the save file

Cloud-hosted images get a new URL on every upload; a replaced card grid means every deck and card
built from it must be re-pointed. Today this is Find & Replace in the save JSON by hand.

- A command "Replace image URL": old URL → new URL across the save file (`*.json` in the Saves folder;
  TTS must not have the save open, or it must be reloaded afterwards), with a count of replacements and
  a backup copy. Candidate URLs listed from the save (custom decks, custom images, tokens, UI assets).
- UI images (`<Image>` assets in the XML / custom assets table) to be looked at when this is done.

## 3a. Quiet messages (with 1.1.0, small)

TTS autosaves every five minutes and reports it (message 6, logged as `SAVED`); with `show_panel_on_output` the panel pops up each time. New setting `panel_quiet_messages` (default `["saved"]`): kinds of messages that are logged but never raise the panel. Candidates: `saved`, `info`, the unsolicited script list.

## 4. Console input (cheap, low priority)

Two-way panel: a line typed into the TTS output panel is sent as a custom message to the mod
(`onExternalMessage`), like VSCode's `onExternalCommand`. 20 lines on the package side.

## Not planned

Lua/XML autocompletion and API docs (large; the API file could still feed a generated
`.sublime-completions` later), asset downloader, git guide, debugging guide, "Create XML on Get" (UI
files are already paired).
