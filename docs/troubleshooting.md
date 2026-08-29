# Troubleshooting

## Decision flow

```mermaid
flowchart TD
  A[Files hidden or re-hiding] --> Where{Desktop/Documents or other folder?}
  Where -->|Desktop/Documents| B[Run Diagnose or Full Report — iCloud Tools]
  Where -->|Other local folder| L[Enable Local assist → Local Tools]
  B --> C{Conflict folders?}
  C -->|yes| D[Scan then Apply or Resolve]
  C -->|no| E{FDA / path errors?}
  E -->|yes| F[Fix FDA — see below]
  E -->|no| G[Likely iCloud sync — app is mitigating symptoms only]
  L --> LD[Diagnose → Scan → Apply]
  D --> H[Verify after 1–2 days]
```

See [Process Flows](process-flows.md) for full diagrams.

## Files keep re-hiding

CloudUnhideWatcher clears local hidden flags; it does **not** fix upstream iCloud sync conflicts. If files re-hide within seconds, iCloud or another process is re-applying `UF_HIDDEN`.

Work through these steps in order:

### 1. Run diagnose (read-only)

**Menu:** iCloud Tools → **Diagnose iCloud Health…** or **Full Report…**

Or from Terminal:

```bash
TOOLKIT="/Applications/CloudUnhideWatcher.app/Contents/Resources/Scripts/icloud_conflict_toolkit.sh"
bash "${TOOLKIT}" diagnose
bash "${TOOLKIT}" report    # diagnose + scan
```

The report checks install path, FDA/CloudDocs access, hidden watch roots, hidden files, conflict folders, and log contention. Output is saved under `~/Library/Logs/CloudUnhideWatcher/`. See [fictional sample output](toolkit-reports-and-dialogs.md#sample-toolkit-reports-fictional).

### 2. Check for conflict folders

Look for folders named like:

- `Desktop - <hostname>`
- `Documents - <hostname>`

These indicate multi-Mac iCloud Desktop & Documents conflicts. Use [iCloud Tools](icloud-tools.md) to preview and merge.

### 3. Merge conflict folders

| Mode | Action |
|------|--------|
| **Scan** | Lists UNIQUE / IDENTICAL / DIFFERS; moves nothing |
| **Apply** | Moves UNIQUE files only; never overwrites live files |
| **Verify** | Compares conflict folders to saved snapshot; detects regrowth |
| **Resolve** | Interactive Terminal session for DIFFERS files — see [Toolkit Reports & Dialogs](toolkit-reports-and-dialogs.md#resolve-conflicting-files--confirmation) if Terminal automation fails |

The toolkit **never deletes** conflict folders automatically. **Apply** never overwrites existing live files. Run **Verify** again after a day or two; **REGROWN** means another Mac is still writing into the conflict copy — repeat on **each Mac** on the same iCloud account.

### 4. When to stop chasing app bugs

If diagnose shows low contention but Finder still flickers, or scan reports many **DIFFERS** rows, treat it as an iCloud sync problem (multi-Mac conflicts, dataless placeholders, materialization failures) — not a CloudUnhideWatcher defect.

## Local (non-iCloud) hidden files

For files **outside** Desktop/Documents (project folders, Downloads subtrees, etc.):

1. **Settings → Assist with local hidden files** — enable
2. **Add Folder…** — path must be under `$HOME`, not a system folder, not iCloud Desktop/Documents
3. **Local Tools → Full Local Report…** — read-only check
4. **Scan Local Folders (dry run)…** then **Apply Local Restore…** if needed
5. For ongoing protection: **Continuously monitor selected local folders**

```bash
TOOLKIT="/Applications/CloudUnhideWatcher.app/Contents/Resources/Scripts/local_hidden_toolkit.sh"
bash "${TOOLKIT}" report --paths-file ~/my-local-paths.txt
```

| Symptom | Likely cause | Action |
|---------|--------------|--------|
| Local Tools menu missing | Assist toggle off | Enable in Settings |
| “Cannot Add Folder” | Path outside home or overlaps iCloud | Pick a different folder |
| Dotfiles still hidden | By design | Do not expect restore — rename manually if needed |
| `.hidden` manifest reported | Legacy Unix hide list | Edit manifest manually — app does not modify it |

See [Local hidden files](local-hidden-files.md) and [Process Flows — Local assist](process-flows.md#local-hidden-file-assist-v130).

## Full Disk Access issues

| Symptom | Fix |
|---------|-----|
| Menu shows "Needs Full Disk Access" | Enable FDA for `/Applications/CloudUnhideWatcher.app` |
| App not listed in FDA | Click **+**, select the installed app; do not run `swift run` |
| FDA enabled but still denied | Reset TCC: `tccutil reset SystemPolicyAllFiles com.cloudunhidewatcher`, re-grant, relaunch |
| ENOTDIR / path errors | Use signed build from `/Applications/`; ensure iCloud Desktop & Documents is enabled |

## Hidden Desktop or Documents root

If the entire Desktop or Documents folder is hidden in Finder, the app clears hidden state on watch roots as well as children. Run **Scan Now** after granting FDA.

## High CPU during heavy activity

The app rescans the full tree on coalesced FSEvents. Very large folders with frequent unrelated activity may use more CPU. Pause monitoring during bulk downloads to Desktop if needed.

## Logs

- File log: `~/Library/Logs/icloud-unhide-watcher.log`
- iCloud Tools reports: `~/Library/Logs/CloudUnhideWatcher/`
- Console: filter `com.cloudunhidewatcher`

## Related

- [Process Flows](process-flows.md)
- [iCloud Tools](icloud-tools.md)
- [Getting Started](getting-started.md)
- [Operator Guide](operator-guide.md)
