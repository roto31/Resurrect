# CloudUnhideWatcher vs similar tools

Honest comparison for operators deciding whether this app fits their workflow.

## Comparison table

| Tool / approach | What it does | CloudUnhideWatcher overlap |
|-----------------|--------------|----------------------------|
| **CloudUnhideWatcher** | Watches iCloud Desktop/Documents for wrongly hidden files; optional iCloud conflict toolkit; optional local hidden assist | — |
| **Hidden Files Toggle** (and similar menu-bar toggles) | Shows/hides dotfiles globally via `chflags` | Does not watch iCloud continuously or merge conflict folders |
| **Manual `chflags nohidden`** | One-off unhide on chosen paths | No monitoring, no conflict-folder workflows |
| **iCloud Sync Doctor** (third-party) | Deep `bird` / brctl diagnostics | Different focus — sync daemon health, not UF_HIDDEN + conflict merge |
| **icloud-tools CLI** (shell) | Power-user conflict toolkit | Same engine as bundled **iCloud Tools**; app adds menu bar watcher + guided Repair |

## When to use CloudUnhideWatcher

- iCloud Desktop/Documents files keep disappearing from Finder because of hidden flags.
- You have `Desktop - <hostname>` / `Documents - <hostname>` conflict folders after sync issues.
- You want a menu-bar watcher plus guided **Repair iCloud Folders** without living in Terminal.

## When **not** to use this app

- General iCloud sync outages with no hidden-flag or conflict-folder symptoms — fix Apple ID, network, or storage first.
- You only need to toggle visibility of dotfiles occasionally — a simple hidden-files toggle is enough.
- You need guaranteed repair of Apple sync daemon state — this app does not modify `bird` or CloudDocs internals.
- You expect automatic overwrite of conflicting file versions without review — **Resolve** always asks per file.

## Related documentation

- [Getting Started](getting-started.md)
- [iCloud Tools — Repair vs Advanced](icloud-tools.md#repair-vs-advanced)
- [In-app Help](getting-started.md#in-app-help-)
