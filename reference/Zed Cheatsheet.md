---

## title: Zed vim mode — sidebar & file navigation cheatsheet date: 2026-08-11 tags: [zed, vim, editor, cheatsheet, keybindings] platform: windows

# Zed vim mode — sidebar & file navigation

Keys below are **Windows/Linux**. On macOS swap `ctrl` → `cmd` for the workspace-level bindings (the panel bindings are unmodified keys and identical everywhere).

The project panel's vim keymap is modeled on **netrw**, not nvim-tree — `%` for a new file, `d` for a new directory, `-` to go up a level.

## Getting in and out

|Key|Action|
|---|---|
|`ctrl-b`|Show/hide the left dock|
|`ctrl-shift-e`|Focus the project panel; from the editor, also reveals the current file|
|`escape`|Toggle focus back to the editor|
|`ctrl-shift-o`|Outline panel|
|`ctrl-p`|File finder (usually faster than the panel for "just open a file")|
|`ctrl-shift-f`|Project-wide search|
|`ctrl-k ctrl-s`|Open the keymap editor to inspect or rebind anything here|

## Project panel — navigation

|Key|Action|
|---|---|
|`j` / `k`|Down / up|
|`g g` / `shift-g`|First / last entry|
|`h`|Collapse selected entry|
|`l`|Expand selected entry|
|`ctrl-left`|Collapse **all** entries|
|`-`|Select parent directory|
|`{` / `}`|Previous / next directory|
|`ctrl-u` / `ctrl-d`|Scroll up / down|
|`z t` / `z z` / `z b`|Scroll cursor to top / center / bottom|
|`] c` / `[ c`|Next / previous git-changed entry|
|`] d` / `[ d`|Next / previous entry with diagnostics|
|`shift-j` / `shift-k`|Extend selection (mark multiple entries)|

## Project panel — opening files

> ⚠️ `o` is **split horizontal**, not "open". This is the main divergence from nvim-tree, where `o` opens the file.

|Key|Action|
|---|---|
|`enter` or `t`|Open permanently (real tab)|
|`p`|Open as preview tab (italic title, replaced by the next preview)|
|`v`|Open in a vertical split|
|`o`|Open in a horizontal split|
|`s`|Open with the system default app|
|`x`|Reveal in File Explorer|

## Project panel — file management

|Key|Action|
|---|---|
|`%`|New file|
|`d`|New directory|
|`shift-r`|Rename|
|`shift-d`|Delete|
|`/`|New search scoped to the selected directory|
|`z d`|Diff two marked files|
|`:`|Command palette, straight from the panel|

Clipboard actions still use the non-vim bindings: `ctrl-x` cut, `ctrl-c` copy, `ctrl-v` paste, `ctrl-z` / `ctrl-shift-z` undo/redo the file operation, `shift-alt-c` copy absolute path, `ctrl-k ctrl-shift-c` copy relative path.

## Outline panel (`ctrl-shift-o`)

|Key|Action|
|---|---|
|`j` / `k`|Down / up|
|`h` / `l`|Collapse / expand|
|`g g` / `shift-g`|First / last|
|`-`|Select parent|
|`enter`|Jump to the symbol and focus the editor|
|`ctrl-u` / `ctrl-d`|Scroll|
|`z t` / `z z` / `z b`|Reposition cursor line|

## Git panel

|Key|Action|
|---|---|
|`j` / `k`|Down / up|
|`g g` / `shift-g`|First / last|
|`g f`|Open the selected change|
|`x`|Stage / unstage the entry|
|`shift-x`|Stage all|
|`shift-u`|Unstage all|
|`g x`|Stage a range|
|`i`|Focus the commit message editor|

## Without vim mode (for reference)

If you ever toggle vim mode off, the panel reverts to: `↑`/`↓` to move, `←`/`→` to collapse/expand, **`space` to open**, and **`enter`/`f2` to rename** — note that `enter` means rename there, which is the opposite of the vim keymap.

## Notes

- These come from Zed's default keymaps (`vim.json` layered over `default-windows.json`), so a Zed upgrade can shift them. `ctrl-k ctrl-s` is the source of truth on your machine.
- Panel vim bindings apply in the `ProjectPanel && not_editing` context — while renaming, the keys are literal text again.