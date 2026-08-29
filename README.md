# Resurrect

**Resurrect** is a macOS menu bar app that watches iCloud Drive Desktop & Documents and automatically clears the hidden flag from user files that iCloud has marked hidden — typically after a sync conflict or a failed/evicted-file (dataless) materialization.

**This is a mitigation for a symptom, not a fix for the underlying iCloud sync problem.** If files keep re-hiding, see [Troubleshooting](docs/troubleshooting.md).

## Download

Install from [GitHub Releases](https://github.com/roto31/Resurrect/releases):

1. Download `Resurrect-<version>-macos.dmg`
2. Open the DMG and drag **Resurrect** to Applications
3. Launch from Applications (macOS 13+)

Verify the SHA-256 checksum in the release assets before installing when checksums are published.

## Quick start

1. Install **Resurrect** to `/Applications/`
2. Launch the app — a phoenix icon appears in the menu bar
3. Grant **Full Disk Access** when prompted (see [Getting Started](https://github.com/roto31/Resurrect/wiki/Getting-Started))
4. The app watches Desktop and Documents and unhides eligible files automatically

See the [Getting Started](https://github.com/roto31/Resurrect/wiki/Getting-Started) wiki page for the full walkthrough.

## Documentation

| Resource | Link |
|----------|------|
| **GitHub Wiki** | [github.com/roto31/Resurrect/wiki](https://github.com/roto31/Resurrect/wiki) |
| **Docs folder** | [docs/README.md](docs/README.md) |
| **Operator guide** | [docs/operator-guide.md](docs/operator-guide.md) |
| **iCloud Tools** | [docs/icloud-tools.md](docs/icloud-tools.md) |
| **Troubleshooting** | [docs/troubleshooting.md](docs/troubleshooting.md) |
| **Changelog** | [CHANGELOG.md](CHANGELOG.md) |

## Requirements

| Requirement | Notes |
|-------------|-------|
| macOS | 13 (Ventura) or later |
| iCloud Drive | Desktop & Documents sync enabled |
| Full Disk Access | Recommended for reliable access to iCloud container paths |
| Distribution | Developer ID signed + notarized builds from Releases |

## What it does

- Watches iCloud Desktop and Documents via `FSEvents`
- Clears Finder `isHidden` and BSD `UF_HIDDEN` on eligible user files
- Skips dotfiles, `.icloud` placeholders, and system files
- Menu bar status, manual scan, pause, settings, and launch-at-login
- **iCloud Tools** submenu runs bundled diagnose and conflict-merge helpers

## License

Proprietary — see [Proprietary Notice](docs/legal/proprietary-notice.md). Source is not published in this repository.
