# Architecture

High-level design of **CloudUnhideWatcher** (consumer view).

## System context

```mermaid
flowchart TB
  subgraph macOS [macOS]
    Menu[Menu bar app]
    Core[CloudUnhideCore library]
    Menu --> Core
  end
  subgraph paths [Watched paths]
    Desktop[iCloud Desktop]
    Documents[iCloud Documents]
    LocalPaths[Local folders — opt-in v1.3.0+]
  end
  subgraph external [External]
    iCloud[iCloud sync / fileproviderd]
    Finder[Finder visibility]
  end
  Core --> Desktop
  Core --> Documents
  Core --> LocalPaths
  iCloud --> Desktop
  iCloud --> Documents
  Core --> Finder
```

## Components

| Component | Responsibility |
|-----------|----------------|
| App lifecycle | Wires monitor, restorer, menu bar, permission probes, debounced rescans |
| Filesystem watcher | `FSEventStream` recursive watch on Desktop & Documents with restart/backoff |
| Restore logic | Eligibility checks, dual-clear hidden flags, pin watchdog for contested paths |
| Permission probe | Detects denied CloudDocs access; surfaces FDA guidance |
| File logger | Optional append-only log at `~/Library/Logs/icloud-unhide-watcher.log` |
| Menu bar UI | `NSStatusItem` + menu (no Dock icon) |
| Login item | `SMAppService` launch-at-login (macOS 13+) |
| iCloud Tools | Runs bundled **iCloud Conflict Toolkit** from app Resources |
| Local Tools | Runs bundled **local hidden toolkit** when assist is enabled (v1.3.0+) |
| LocalWatchResolver | Validates user local paths under `$HOME`; excludes system prefixes and iCloud overlap |

Core logic lives in a testable library; the menu bar app is a thin AppKit shell.

## Event flow

```mermaid
flowchart TD
  FSEvents[FSEvents on Desktop/Documents + optional local paths]
  Debounce[Debounce ~1.5s]
  Scan[Full recursive scan]
  Eligible{Eligible user file?}
  Clear[Clear isHidden + UF_HIDDEN]
  Pin[Pin watchdog if re-hidden]
  FSEvents --> Debounce --> Scan --> Eligible
  Eligible -->|yes| Clear
  Clear --> Pin
```

Local folders join the FSEvents stream only when **Assist with local hidden files** and **Continuously monitor selected local folders** are both enabled in Settings. See [Local hidden files](local-hidden-files.md).

FSEvents fire on many change types, not only hidden-flag changes. The app rescans the watched trees rather than interpreting individual event flags — simple and robust for iCloud sync bursts.

## Dual-clear mechanism

```mermaid
sequenceDiagram
  participant R as Restorer
  participant F as Finder metadata
  participant B as BSD flags
  R->>F: setResourceValues isHidden=false
  R->>B: chflags nouchg,nohidden
  Note over R,B: Both layers cleared — iCloud may still re-hide
```

## Watch paths

Default watch roots resolve to the user's iCloud Desktop and Documents folders (including when the File Provider exposes detached symlinks). Watch roots themselves are cleared if hidden so a hidden root does not hide the entire tree in Finder.

## Eligibility policy

Restored paths must be:

- Under configured watch roots
- Regular files or directories (not placeholders)
- Not dot-prefixed names
- Not excluded system names (`.DS_Store`, `.Trash`, `.icloud`, etc.)

## Failure handling

- FSEvent stream start failures → exponential backoff restart (cap 30s), status in menu bar
- Permission denial → FDA prompt and settings shortcuts
- iCloud re-hide → pin watchdog (1s interval, max 120s, release after clean checks)

## Distribution

- Developer ID signed + notarized `.app` in release DMG
- Unsandboxed (required for Apple's iCloud container)
- Bundle ID: `com.ruter.cloudunhidewatcher`

## Related

- [Process Flows](process-flows.md)
- [Local hidden files](local-hidden-files.md)
- [Project Overview](project-overview.md)
- [Operator Guide](operator-guide.md)
