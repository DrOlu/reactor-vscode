# Reactor

[![Open VSX](https://img.shields.io/open-vsx/v/HyperspaceTechnologies/reactor-vscode?label=Open%20VSX&color=purple)](https://open-vsx.org/extension/HyperspaceTechnologies/reactor-vscode)

VS Code extension for [Reactor coding agent](https://pi.dev/) by [Hyperspace Technologies](https://hyperspace.ng).

## Features

- **Terminal-based** — Opens Reactor as an integrated terminal with full TUI/PTY support (opens beside the editor)
- **VS Code bridge** — Bundles a Reactor extension and local bridge so Reactor can query live editor state
- **Editor awareness** — Reactor can inspect the active editor, current/latest selection, open editors, workspace folders, and VS Code diagnostics (LSP / lint / type errors)
- **Status bar button** — Reactor button in the status bar for quick access
- **Open with file context** — Send current file path and line range (or cursor position) to Reactor, available from the editor title bar
- **Send selection** — Send selected text directly to the Reactor terminal
- **`@pi` chat participant** — Use `@pi` in VS Code Chat for streamed RPC-backed replies while keeping the terminal workflow for normal Reactor sessions
- **Package manager** — Browse, search, install, and uninstall Reactor packages from the sidebar with live output streaming and cancel support; automatically detects package capabilities (extensions, skills, prompts, themes)
- **Auto-detection** — Finds the Reactor binary automatically from common paths (`~/.bun/bin`, `~/.local/bin`, `~/.npm-global/bin`)

## Requirements

- `pi` CLI installed (`npm i -g @mariozechner/pi-coding-agent` or `bun i -g @mariozechner/pi-coding-agent`)
- An API key configured for at least one provider

## Install

Available on [Open VSX](https://open-vsx.org/extension/HyperspaceTechnologies/reactor-vscode):

```bash
# VS Code / Cursor
ext install HyperspaceTechnologies.reactor-vscode

# Open VSX (VSCodium, etc.)
ovsx get HyperspaceTechnologies.reactor-vscode
```

Or download the `.vsix` directly from [GitHub Releases](https://github.com/DrOlu/reactor-vscode/releases):

```bash
code --install-extension reactor-vscode-0.0.8.vsix
```

## Commands

| Command                    | Keybinding       | Description                              |
| -------------------------- | ---------------- | ---------------------------------------- |
| `Reactor: Open`            | `Ctrl+Alt+3`     | Open or focus the Reactor terminal       |
| `Reactor: Open with File`  | Editor title bar | Open Reactor with current file context   |
| `Reactor: Send Selection`  | —                | Send selected text to Reactor terminal   |

## Sidebar

The **Reactor** activity bar icon opens a sidebar with:

- **Packages view** — Search the npm registry for `pi-package` packages, see capability labels (extensions, skills, prompts, themes), install/uninstall with live streamed output, and cancel in-progress operations

## Bridge tools exposed to Reactor

Each Reactor terminal launched by the extension loads a bundled extension that can call back into live VS Code APIs.

### Inspection tools

| Tool                           | What it returns                                                                                                               |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| `vscode_get_editor_state`      | Aggregate snapshot of workspace folders, active editor metadata, current selection, latest cached selection, and open editors |
| `vscode_get_selection`         | Current active editor selection including selected text, file path, and coordinates                                           |
| `vscode_get_latest_selection`  | Most recent cached selection seen by the extension, even if focus already moved                                               |
| `vscode_get_diagnostics`       | VS Code diagnostics for a specific file or the whole workspace                                                                |
| `vscode_get_open_editors`      | Visible/open file editors with language, dirty state, and active flag                                                         |
| `vscode_get_workspace_folders` | Workspace folders for the current VS Code window                                                                              |
| `vscode_get_document_symbols`  | Outline symbols for a file from the active language server                                                                    |
| `vscode_get_definitions`       | Symbol definition locations at a given file position                                                                          |
| `vscode_get_type_definitions`  | Symbol type-definition locations at a given file position                                                                     |
| `vscode_get_implementations`   | Concrete implementation locations for an interface or abstract member                                                         |
| `vscode_get_declarations`      | Symbol declaration locations at a given file position                                                                         |
| `vscode_get_hover`             | Hover docs, inferred types, signatures, and markdown/code snippets from the language server                                   |
| `vscode_get_workspace_symbols` | Global workspace symbol search through VS Code language providers                                                             |
| `vscode_get_references`        | Symbol references at a given file position                                                                                    |
| `vscode_get_code_actions`      | Available code actions / quick fixes for a selection or explicit range, plus intersecting diagnostics                         |
| `vscode_get_notifications`     | Buffered bridge events such as selection, editor, diagnostics, save, and dirty-state changes                                  |

### Action tools

| Tool                          | What it does                                                                                     |
| ----------------------------- | ------------------------------------------------------------------------------------------------ |
| `vscode_open_file`            | Opens a file in VS Code and can reveal/select a range                                            |
| `vscode_check_document_dirty` | Checks whether a file is open and whether it has unsaved changes                                 |
| `vscode_save_document`        | Saves a document through VS Code                                                                 |
| `vscode_execute_code_action`  | Executes a previously returned code action by `actionId`                                         |
| `vscode_apply_workspace_edit` | Applies explicit range-based text replacements through VS Code so open buffers stay synchronized |
| `vscode_format_document`      | Runs the active document formatter for a file and applies the resulting edits through VS Code    |
| `vscode_format_range`         | Runs the active range formatter for a selection/range and applies the resulting edits            |
| `vscode_clear_notifications`  | Clears the buffered bridge notification queue                                                    |
| `vscode_show_notification`    | Shows an info, warning, or error notification inside VS Code                                     |

These bridge tools let Reactor inspect selections, diagnostics, symbols, definitions, declarations, implementations, hover/type info, workspace-wide symbol search, references, quick-fix availability, dirty state, and recent IDE events, while also safely opening files, saving buffers, applying workspace edits, formatting open buffers through VS Code providers, running VS Code code actions, and surfacing notifications back to the user.

## Configuration

| Setting             | Default | Description                                                              |
| ------------------- | ------- | ------------------------------------------------------------------------ |
| `pi-vscode.path`    | `""`    | Absolute path to the Reactor binary (auto-detected if empty)             |
