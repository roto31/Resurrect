# Menu Bar Navigation

CloudUnhideWatcher runs as a **menu bar only** app (no Dock icon). Click the **eye** icon in the menu bar to open the main menu.

## Main menu

![CloudUnhideWatcher main menu — status, Scan Now, Pause, iCloud Tools, FDA, Settings, Quit](images/menu-bar-main.jpg)

| Item | Shortcut | What it does | Opens |
|------|----------|--------------|-------|
| **Status line** | — | Gray header: monitoring state, last restore count, paused, or FDA needed. | Nothing (not clickable). |
| **Scan Now** | ⌘S | Immediate full scan of watched iCloud Desktop/Documents; clears hidden flags on eligible paths. | Nothing; may update status line and app log. |
| **Pause Monitoring** | ⌘P | Stops FSEvents watching without quitting. Label toggles to resume when paused. | Nothing. |
| **iCloud Tools** | → | Submenu — bundled **iCloud Conflict Toolkit**. | Submenu (below). |
| **Local Tools** | → | Submenu — **local hidden file** toolkit (when feature is enabled in your build). | Submenu (below). |
| **Enable CloudUnhideWatcher in Full Disk Access…** | — | Shown when CloudDocs paths are not readable. | **System Settings → Privacy & Security → Full Disk Access**. |
| **Settings…** | ⌘, | Preferences window. | [Settings window](toolkit-reports-and-dialogs.md#settings-window). |
| **Quit CloudUnhideWatcher** | ⌘Q | Exits and stops all monitoring. | Nothing. |

### Status line examples

| Message | Meaning |
|---------|---------|
| `Monitoring active — clean as of …` | Watching; no hidden files restored in the last scan |
| `Restored N file(s)…` | Last scan cleared hidden flags on N paths |
| `Paused` | Monitoring suspended via **Pause Monitoring** |
| `Needs Full Disk Access` (or similar) | Grant FDA before scans can reach iCloud folders |

## iCloud Tools submenu

![iCloud Tools submenu — diagnose, report, scan, apply, verify, resolve](images/menu-bar-icloud-tools.jpg)

| Item | Toolkit command | What it does | When finished |
|------|-----------------|--------------|---------------|
| **Diagnose iCloud Health…** | `diagnose` | Read-only: install, FDA, hidden roots/files, conflict folders, log contention. | [Result sheet](toolkit-reports-and-dialogs.md#result-sheet-after-diagnose-scan-apply-verify-report) + log file. |
| **Full Report (diagnose + scan)…** | `report` | Diagnose plus conflict-folder dry run in one log. | Result sheet + log file. |
| *(separator)* | — | — | — |
| **Scan Conflict Folders (dry run)…** | `scan` | Lists UNIQUE / IDENTICAL / DIFFERS; moves nothing. | Result sheet + log file. |
| **Apply Conflict Merge…** | `apply` | Moves **UNIQUE** files into live folders only; saves verify snapshot. | **Continue/Cancel** confirm first, then result sheet + log. |
| **Verify Conflict Folders…** | `verify` | Compares to last apply snapshot; flags **REGROWN** folders. | Result sheet + log file. |
| **Resolve Conflicting Files…** | `resolve` | Interactive **DIFFERS** resolution. | [Resolve dialog](toolkit-reports-and-dialogs.md#resolve-conflicting-files--confirmation) → **Terminal** (or [automation error](toolkit-reports-and-dialogs.md#resolve-failed--terminal-automation)). |

If another toolkit job is already running, you see **[Maintenance Busy](toolkit-reports-and-dialogs.md#maintenance-busy)** instead.

Menu items run the bundled script inside the app bundle with `CUW_MAINTENANCE_FROM_APP=1` so diagnose does not false-fail “app not running.”

## Local Tools submenu

Available when **Assist with local hidden files** is enabled in Settings (non-iCloud folders only).

| Item | Toolkit command | What it does | When finished |
|------|-----------------|--------------|---------------|
| **Diagnose Local Hidden Files…** | `diagnose` | Checks saved local watch paths for hidden-file issues. | Result sheet + `local_hidden_toolkit.sh.*.log`. |
| **Scan Local Folders (dry run)…** | `scan` | Lists hidden candidates; changes nothing. | Result sheet + log. |
| **Apply Local Restore…** | `apply` | Clears hidden flags on eligible files (not dotfiles). | **Continue/Cancel** confirm, then result sheet + log. |
| **Full Local Report…** | `report` | Diagnose + scan for local paths in one log. | Result sheet + log. |

If no folders are saved in Settings, **Apply** and scan-style actions open an **NSOpenPanel** folder picker first.

## Settings window

![Settings window](images/settings-window.jpg)

See [Toolkit Reports & Dialogs — Settings](toolkit-reports-and-dialogs.md#settings-window) for every control and what each button opens.

## Result sheets and alerts

Screenshots and button reference:

| Topic | Link |
|-------|------|
| Scan / Diagnose result sheet | [Result sheet](toolkit-reports-and-dialogs.md#result-sheet-after-diagnose-scan-apply-verify-report) |
| Maintenance Busy | [Maintenance Busy](toolkit-reports-and-dialogs.md#maintenance-busy) |
| Resolve confirmation & Terminal | [Resolve dialogs](toolkit-reports-and-dialogs.md#resolve-conflicting-files--confirmation) |
| Fictional sample log output | [Sample reports](toolkit-reports-and-dialogs.md#sample-toolkit-reports-fictional) |

## Typical navigation paths

### First install

1. Main menu → **Enable CloudUnhideWatcher in Full Disk Access…**
2. Grant FDA in System Settings, relaunch
3. Main menu → **Settings…** → enable **Launch at Login** (optional)

### Files keep re-hiding

1. **iCloud Tools → Diagnose iCloud Health…** or **Full Report…**
2. If conflict folders appear → **Scan Conflict Folders (dry run)…**
3. If mostly UNIQUE → **Apply Conflict Merge…**
4. Days later → **Verify Conflict Folders…**
5. If DIFFERS rows → **Resolve Conflicting Files…** (Terminal)

### Quick manual unhide

1. **Scan Now** (⌘S) after granting FDA

## Keyboard shortcuts summary

| Shortcut | Action |
|----------|--------|
| ⌘S | Scan Now |
| ⌘P | Pause Monitoring |
| ⌘, | Settings |
| ⌘Q | Quit |

## Related

- [Toolkit Reports & Dialogs](toolkit-reports-and-dialogs.md) — screenshots, buttons, sample logs
- [Operator Guide](operator-guide.md) — settings detail and eligibility policy
- [iCloud Tools](icloud-tools.md) — toolkit commands and safety rules
- [Getting Started](getting-started.md) — install and FDA
- [Troubleshooting](troubleshooting.md) — re-hide and conflict workflows
