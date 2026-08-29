# Operator Guide

For screenshots and a full menu walkthrough, see **[Menu Bar Navigation](navigation.md)**.

## Menu bar

Click the eye icon in the menu bar:

![Main menu](images/menu-bar-main.jpg)

| Item | Action |
|------|--------|
| Status line | Current state (idle, restored count, paused, FDA needed) |
| **Scan Now** | Immediate full scan of watched folders |
| **iCloud Tools** | Run bundled diagnose / conflict-merge scripts |
| **Pause Monitoring** | Stop FSEvents without quitting |
| **Enable CloudUnhideWatcher in Full Disk Access…** | Opens Privacy & Security (when access denied) |
| **Settings…** (⌘,) | Preferences window |
| **Quit** | Exit the app |

## Settings

Open **Settings…** from the menu or press ⌘,.

### General

- **Watched folders** — defaults to iCloud Desktop and Documents; adjust only if you understand the paths
- **Launch at Login** — register with `SMAppService` (macOS 13+)

### Monitoring

- **Watch iCloud Desktop / Documents** — default on; core iCloud monitoring
- **Scan debounce** — delay before rescanning after FSEvents bursts (default reduces thrash during iCloud sync)
- **FSEvents latency** — coalescing window for filesystem events

### Local hidden files (non-iCloud) — v1.3.0+

Opt-in section below **Monitoring**. Default **off**.

| Control | Purpose |
|---------|---------|
| **Assist with local hidden files** | Master toggle — shows **Local Tools** menu and folder list |
| **Add Folder… / Remove** | Up to 10 folders under your home directory (validated) |
| **Continuously monitor selected local folders** | Adds saved paths to FSEvents and **Scan Now** |

Does **not** fix dotfiles, `.hidden` manifests, or iCloud conflict folders. See [Local hidden files](local-hidden-files.md) and [Process Flows](process-flows.md#local-hidden-file-assist-v130).

### Logging

- **File logging** — append to `~/Library/Logs/icloud-unhide-watcher.log`
- Unified logging also available in Console.app: subsystem `com.ruter.cloudunhidewatcher`

### Privacy

- **Open Full Disk Access Settings…**
- **Reveal CloudUnhideWatcher in Finder** — locate the app for the FDA **+** picker

## iCloud Tools

![iCloud Tools submenu](images/menu-bar-icloud-tools.jpg)

The **iCloud Tools** submenu runs maintenance scripts bundled inside the app. Reports are saved under `~/Library/Logs/CloudUnhideWatcher/`.

See [Menu Bar Navigation](navigation.md#icloud-tools-submenu) and [iCloud Tools](icloud-tools.md) for each menu item and toolkit command.

## Local Tools (optional)

When **Assist with local hidden files** is enabled in Settings, the **Local Tools** submenu runs `local_hidden_toolkit.sh` for folders outside iCloud Desktop/Documents.

See [Local hidden files](local-hidden-files.md), [Menu Bar Navigation — Local Tools](navigation.md#local-tools-submenu), and [Process Flows — Local assist](process-flows.md#local-hidden-file-assist-v130).

## What gets unhidden

The app clears hidden flags only on **eligible user files**:

- Regular files and folders under Desktop/Documents **and** (when enabled) saved local watch paths
- Not dotfiles (names starting with `.`)
- Not `.icloud` placeholders or known system files (e.g. `.DS_Store`, `.Trash`)

When iCloud repeatedly re-applies the hidden flag, the app uses a short pin watchdog (1s re-clears, up to 120s) on contested paths.

## MDM deployment

For managed Macs, pre-approve Full Disk Access via a PPPC configuration profile for bundle ID `com.ruter.cloudunhidewatcher` rather than relying on interactive prompts.

## Related

- [Menu Bar Navigation](navigation.md)
- [Local hidden files](local-hidden-files.md)
- [Getting Started](getting-started.md)
- [Troubleshooting](troubleshooting.md)
- [Architecture](architecture.md)
