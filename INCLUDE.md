# Include

Since TTS does not support the require or similar functions of Lua, the package provides a similar functionality. It is a mere text injection of the included files into the main file and does not create a module table like require. If this is wanted it needs to be implemented in the module files explicitely.

The whole file can be previewed in Sublime (e.g. in order to find the correct line numbers for errors) and even edited in this state, and afterwards safely split again.

This is a generic function and works on any Lua file (e.g. also object scripts).

## Detailed Function

* The "include" commands should be placed above any other code.
* The found files are included in the specified order after their include line, which is kept.
* The same file is only included once (first call).
* Dependencies (other includes) in an included file are also included recursively and inserted at a position before where the calling file was inserted.
* If a function of a module is present in any previously included (already processed) module or the core file, it is outcommented. Therefore it is recommended to keep the documentation inside the function declaration.
* Local or global variables are inserted unaltered. Since an included module is inserted above the calling module the same logic applies to them as for functions: a variable or function nearer to the core file (or in the core file) wins over one further down.
* When saving to TTS the files are automatically joined. When manually loading the scripts from TTS, they are automatically split into modules and compared with the saved data, prompting to override if they do not match.

## Syntax

`-- includepath "?; ?.lua; ../lib/?.lua; c:/lib/?.lua; usr/local/lua/?/?.lua"`
This sets the paths for all includes, which are defined as pattern:
* "?" is replaced with the parameter (file name) given by "include")
* The first matching pattern is used.
* Relative paths are relative to the including file's folder.
* A global path list can be stored in settings (`include_paths`), which defaults to "?; ?.lua".
    * A local "includepath" overrides the "include_paths" in settings.
    * To join both parameters write "&" where you want "include_paths" value to be inserted, usually at the end (e.g. `--includepath "../lib/?.lua; &"` will result in "../lib/?.lua; ?; ?.lua").

`-- include "file"`
The name of the file to be included. If this contains a path the same rules apply as above.

## Merge Format

An included file has the following comment lines before and after. This allows to distinguish between the core code and the included codes and makes it even possible to edit any code in core or includes safely. The comments contain the file name as given in the "include" and store the relative path to the core file’s folder (or full path if necessary).
(Note for reason: The stored path is the resolved path, so a split needs not reversely resolve includes relative to a module.)

```lua
--[[DON'T TOUCH! START INCLUDE: "file", "path relative to core file"]]
...
--[[DON'T TOUCH! END INCLUDE: "file", "path relative to core file"]]
```

An outcommented function looks like this (each line preceded with "--" to be safe on block comments):

```lua
--[[DON'T TOUCH! START DISABLED FUNCTION: "function name", "file"]]
--function name(...)
--...
--end
--[[DON'T TOUCH! END DISABLED FUNCTION: "function name", "file"]]
```

If any those lines are changed or removed the file cannot safely be split into its parts again!!!

## Usage

All items are in Tools > Tabletop Simulator
* **Get Lua Scripts**: The retrieved scripts are scanned for module parts and automatically split, and the modules and the core part are compared with the saved data. If there is a mismatch (or the modules do not exist) a dialog asks to overwrite the saved data or to save them in an folder, which can be specified in an open folder dialog. Each module, the core file and the combined file ("<script name>_Merged.<guid>.lua") is saved. If the chosen folder contains files which would be overwritten another warning dialog comes up (overwrite, change folder, cancel).
* **Save & Play or Build**: Before sending to TTS any include is replaced (if not already included). If a file cannot be found a dialog is showing up (what was not found) and nothing is pushed to TTS. This is done silently and does not show in the editor.
* **File interface**: the commands `saveAndPlay` and `getScripts` dropped into the outbox behave exactly like Save & Play and Get Lua Scripts above (merge before the push, split after the download); a missing include file or a mismatch is reported in the log instead of a dialog.
* **Merge Includes**: The files are included in the editor windows and can be previewed (e.g. to find the line referenced in TTS for a lua error). It is even possible to edit the file (if `include_allow_merged_edit` is enabled) and save all changes. If an include file cannot be found the same happens as for "Save & Play" above.
* **Split Includes**: The file is split into its parts and offered to save if changed:
    * This is done with the information in the start and end comment line (which have the full path) before and after a module (so the module is what is inbetween those lines).
    * Upon split all outcommented functions are restored to their original state.
    * The modules is compared to the saved files and if changed a dialog per module asks to save or not or save for all (except `include_auto_save_modules` is enabled).
    * Then the whole block (including the comment lines) is deleted.
    * This is even possible if the whole was saved as one and should be splitted later. The only thing necessary is that the paths in the comment lines are correct.
* **Save**: Upon saving the merged file (File > Save (or Save as...)) a warning dialog is shown to either continue, save the split files, or cancel. To save without warning enable `include_allow_merged_save`.

## Settings

```JSON
{
    // The path list to look for files to include, separated by semicolon. The ? is a placeholder for the named include file.
    // Always use / even on Windows (e.g. "c:/user/../lib/?.lua")
    "include_paths": "?; ?.lua",
    // Allows the merged file to be edited (true), otherwise (false) it can only be viewed
    "include_allow_merged_edit": false,
    // Allows a merged file to be saved, i.e. it is possible to work on the merged file continuously and split it later.
    // If set to false a warning dialog is shown if a merged file would be saved.
    "include_allow_merged_save": false,
    // Automatically save changed modules without asking
    "include_auto_save_modules": false,
    // A function in a module overruling the same function in an included module can be difficult to debug.
    // Therefore the console issues a warning if this happens. Set to false to disable the warning.
    "include_warn_on_function_override": true,
}
```