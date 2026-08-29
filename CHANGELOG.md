# Changelog

All notable changes to **CloudUnhideWatcher** are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## Documentation

- [Docs index](docs/README.md)
- [Wiki home](https://github.com/roto31/CloudUnhideWatcher/wiki)
- [Getting Started](https://github.com/roto31/CloudUnhideWatcher/wiki/Getting-Started)
- [Troubleshooting](docs/troubleshooting.md)
- [iCloud Tools](docs/icloud-tools.md)

## [Unreleased]

### Added
- Public documentation mirror (`docs/`, GitHub Wiki, Releases on this repository) — see [Docs index](docs/README.md).
- Menu bar **iCloud Tools** submenu runs bundled diagnose and conflict-merge scripts; reports under `~/Library/Logs/CloudUnhideWatcher/` — see [iCloud Tools](docs/icloud-tools.md).
- Conflict-folder warnings in app log after scans when `Desktop - <hostname>` / `Documents - <hostname>` folders exist.

### Changed
- Hidden restore dual-clears Finder `isHidden` and BSD `UF_HIDDEN`, uses a short self-event suppress (~4s), and runs a per-path pin watchdog (1s re-clears up to 120s) when iCloud re-applies the hidden flag — see [Troubleshooting](docs/troubleshooting.md).

### Fixed
- Recursive scan clears hidden state on watch root directories (`~/Desktop`, `~/Documents`).
- Watch Desktop/Documents via resolved user folders when iCloud File Provider exposes detached symlinks (avoids false “Needs Full Disk Access”).
- Full Disk Access alert only shown for real permission denials.

## [1.1.0] - 2026-08-29

### Added
- Settings window (⌘,) for watched folders, debounce, FSEvents latency, file logging, and launch-at-login — see [Operator Guide](docs/operator-guide.md).
- Permission probe and FDA guidance naming **CloudUnhideWatcher** explicitly — see [Getting Started](docs/getting-started.md).
- File logger at `~/Library/Logs/icloud-unhide-watcher.log`.
- FSEvents stream restart with exponential backoff on failures.
- Unit-tested core library for monitor, restorer, and permission logic.
- Custom application icon in release builds.
- Signed and notarized release DMGs on [GitHub Releases](https://github.com/roto31/CloudUnhideWatcher/releases).

### Fixed
- Login item menu reflects actual `SMAppService` registration status.
- Release signing selects the first working Developer ID certificate when duplicates exist in the keychain.

### Changed
- Significant actions log to both unified logging and the file log.

## [1.0.0] - 2026-08-15

### Added
- Initial menu bar app watching iCloud Desktop & Documents via FSEvents.
- Auto-restore hidden flags on eligible user files only.
- Menu with status, Scan Now, pause/resume, launch at login, and quit.
