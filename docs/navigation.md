# Menu Bar Navigation

CloudUnhideWatcher runs as a **menu bar only** app (no Dock icon). Click the **eye** icon in the menu bar to open the main menu.

## Main menu

![CloudUnhideWatcher main menu — status, Scan Now, Pause, iCloud Tools, FDA, Settings, Quit](images/menu-bar-main.jpg)

| Item | Shortcut | Description |
|------|----------|-------------|
| **Status line** | — | Gray header showing current state (e.g. `Monitoring active — clean as of HH:MM:SS`, restored file count, paused, or FDA needed). Not clickable. |
| **Scan Now** | ⌘S | Runs an immediate full scan of watched Desktop/Documents folders and clears hidden flags on eligible paths. |
| **Pause Monitoring** | ⌘P | Stops FSEvents watching without quitting. Status reflects paused state; choose again to resume (label toggles when implemented) or relaunch to resume. |
| **iCloud Tools** | → | Submenu with bundled **iCloud Conflict Toolkit** commands — see below. |
| **Enable CloudUnhideWatcher in Full Disk Access…** | — | Opens **System Settings → Privacy & Security → Full Disk Access**. Shown when CloudDocs paths are not accessible. Hidden when access is OK. |
| **Settings…** | ⌘, | Opens the preferences window (watched folders, debounce, logging, launch at login, privacy shortcuts). |
| **Quit CloudUnhideWatcher** | ⌘Q | Exits the app and stops all monitoring. |

### Status line examples

| Message | Meaning |
|---------|---------|
| `Monitoring active — clean as of …` | Watching; no hidden files restored in the last scan |
| `Restored N file(s)…` | Last scan cleared hidden flags on N paths |
| `Paused` | Monitoring suspended via **Pause Monitoring** |
| `Needs Full Disk Access` (or similar) | Grant FDA before scans can reach iCloud folders |

## iCloud Tools submenu

Hover **iCloud Tools** (or click) to open the toolkit submenu:

![iCloud Tools submenu — diagnose, report, scan, apply, verify, resolve](images/menu-bar-icloud-tools.jpg)

| Item | Toolkit command | What it does |
|------|-----------------|--------------|
| **Diagnose iCloud Health…** | `diagnose` | Read-only health check: install path, FDA, hidden roots/files, conflict folders, log contention. Report in `~/Library/Logs/CloudUnhideWatcher/`. |
| **Full Report (diagnose + scan)…** | `report` | Runs diagnose plus conflict-folder dry run in one step. |
| *(separator)* | — | — |
| **Scan Conflict Folders (dry run)…** | `scan` | Lists UNIQUE / IDENTICAL / DIFFERS files in `Desktop - hostname` / `Documents - hostname` folders; moves nothing. |
| **Apply Conflict Merge…** | `apply` | Moves **UNIQUE** files into live Desktop/Documents only; never overwrites. Saves snapshot for verify. |
| **Verify Conflict Folders…** | `verify` | Compares conflict folders to last apply snapshot; flags **REGROWN** if iCloud refilled them. |
| **Resolve Conflicting Files…** | `resolve` | Opens **Terminal** for interactive resolution of **DIFFERS** files (backs up live copy before overwrite). |

Menu items run the bundled script inside the app bundle and set `CUW_MAINTENANCE_FROM_APP=1` so diagnose does not false-fail “app not running.”

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

## Settings window

Open via **Settings…** (⌘,) from the main menu.

| Section | Controls |
|---------|----------|
| **General** | Watched folder paths, **Launch at Login** |
| **Monitoring** | Scan debounce, FSEvents latency |
| **Logging** | Enable file log at `~/Library/Logs/icloud-unhide-watcher.log` |
| **Privacy** | Open FDA settings, reveal app in Finder for the **+** picker |

Settings are the single place for **Launch at Login** (not duplicated on the root menu).

## Keyboard shortcuts summary

| Shortcut | Action |
|----------|--------|
| ⌘S | Scan Now |
| ⌘P | Pause Monitoring |
| ⌘, | Settings |
| ⌘Q | Quit |

## Related

- [Operator Guide](operator-guide.md) — settings detail and eligibility policy
- [iCloud Tools](icloud-tools.md) — toolkit commands and safety rules
- [Getting Started](getting-started.md) — install and FDA
- [Troubleshooting](troubleshooting.md) — re-hide and conflict workflows
