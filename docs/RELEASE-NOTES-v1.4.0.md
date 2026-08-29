## CloudUnhideWatcher v1.4.0

Premiere-class UX release: guided workflows, in-app conflict resolution, and embedded Help.

### Highlights

- **FDA setup assistant** — guided Full Disk Access wizard on first launch
- **Repair iCloud Folders…** — one recommended path for diagnose + scan + next steps
- **In-app DIFFERS resolver** — review conflicting files without Terminal
- **Help menu (⌘?)** — search embedded documentation offline
- **Advanced iCloud Tools** — power-user commands hidden until enabled

### Breaking change

Bundle ID is now **`com.cloudunhidewatcher`**. After upgrading:

1. Re-grant **Full Disk Access** in System Settings
2. Re-enable **Launch at Login** if used
3. Update MDM PPPC profiles to the new bundle ID

Settings migrate automatically from the legacy UserDefaults prefix.

### Documentation

- [Getting Started](https://github.com/roto31/CloudUnhideWatcher/blob/main/docs/getting-started.md)
- [Menu Bar Navigation](https://github.com/roto31/CloudUnhideWatcher/wiki/Menu-Bar-Navigation)
- [Comparison guide](https://github.com/roto31/CloudUnhideWatcher/blob/main/docs/comparison.md)

Install: download `CloudUnhideWatcher-1.4.0-macos.dmg`, drag to Applications, open once via right-click → Open if needed.
