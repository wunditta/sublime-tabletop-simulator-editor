# Changelog

Format: [Keep a Changelog](https://keepachangelog.com), versions: [Semantic Versioning](https://semver.org).

## [Unreleased]

### Added

- **Settings** opens three columns when a project is open: the package's default settings (read-only), the user settings and the project file — so a project can be configured with the names and comments in view (default | user as before without a project)
- **Save & Play This File**: pushes the active script file
  - menu, command palette, right-click in the text area ("TTS Save & Play")
- **Side-bar Context Menu**
  - on a script file: "TTS Save & Play"
  - on a folder: "TTS Save & Play All Scripts" — the script folder, or any folder with a `Global.-1.lua` after a confirmation (restores a history snapshot in two clicks)
- Menu checkbox **Save Used Files on Build** (`save_used_on_build`): unsaved pushed files are saved without asking
- Key binding **Ctrl+Shift+Alt+S**: Save & Play (all scripts)
- One `Default.sublime-keymap` for all platforms (`primary` = Ctrl / ⌘)

### Changed

- **Tools › Build (Ctrl+B)** pushes *all* scripts and saves per Sublime's "Save All on Build"
  - the package menu pushes the open files and saves per its own checkbox; the two never mix
- Save & Play (all scripts) saves only the script files it pushes, not every open file
- Execute's caption says what it does: "Execute Selection …" with a selection, "Execute File …" without
- Menu entries that need a script file are greyed out while another file is active

### Removed

- Setting `push`: the menu has an entry per mode, Build is the all-scripts push
- Setting `save_before_push`: renamed to `save_used_on_build`

## [v1.0.1] - 2026-11-03

### Fixed

- A plugin reload no longer leaves an orphaned listener on port 39998; a failed bind is retried once

## [v1.0.0] - 2026-11-02

First release.

- Get Lua Scripts, Save & Play (open files or whole folder, Ctrl+B), Execute Selection in Global
- TTS output panel with print output and Lua errors
- Save id in `Global.-1.lua`, checked before a push and before a Get
- History of pushed files with a kept-count
- Listener modes on_demand / first_use / always
- After-push verification against the script list TTS reports
- File interface (command files and log) for automation tools and AI assistants
