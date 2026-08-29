# Local hidden files (non-iCloud)

CloudUnhideWatcher v1.3.0 adds an **opt-in** feature for folders **outside** iCloud Desktop & Documents.

## When to use

Enable **Assist with local hidden files** in Settings when:

- Finder or `chflags hidden` accidentally hid files in a local project folder, Downloads subtree, or other non-iCloud path
- You want diagnose/scan/apply tools for those folders without changing default iCloud-only behavior

## When **not** to use

This feature does **not**:

- Rename or un-hide dotfiles (`.git`, `.env`, etc.) — those are intentionally hidden by convention
- Repair `.hidden` manifest files (diagnose **detects** them; edit manually)
- Merge iCloud conflict folders — use [iCloud Tools](icloud-tools.md) instead
- Watch `/System`, `/Library`, `/Applications`, or your entire home folder

## Settings

| Control | Default | Behavior |
|---------|---------|----------|
| Assist with local hidden files | **Off** | Shows **Local Tools** menu and folder list |
| Folder list | Empty | Up to 10 folders under your home directory |
| Continuously monitor selected local folders | **Off** | Adds saved folders to FSEvents when master toggle is on |

iCloud Desktop/Documents monitoring is unchanged and remains on by default.

## Menu: Local Tools

When the master toggle is on:

- **Diagnose Local Hidden Files** — read-only health check
- **Scan Local Folders (dry run)** — preview what would be restored
- **Apply Local Restore** — clears eligible hidden flags (confirm dialog)
- **Full Local Report** — diagnose + scan

If no folders are saved in Settings, toolkit runs prompt you to pick folders once.

## Full Disk Access

Some local paths (especially outside standard user folders) may require **Full Disk Access** for CloudUnhideWatcher — same as iCloud monitoring. Enable the app in **System Settings → Privacy & Security → Full Disk Access**.

## State and logs

- Toolkit state: `~/.cloudunhide_local_state/`
- Full script output: `~/Library/Logs/CloudUnhideWatcher/local_hidden_toolkit.sh.*.log`

## See also

- [iCloud Tools](icloud-tools.md) — conflict folders and iCloud-specific diagnose
- [README](../../README.md) — eligibility rules (dotfiles, placeholders, protected names)
