## Resurrect v1.5.0

Product rename from CloudUnhideWatcher. This is a **new app identity**.

### Changed

- App name is **Resurrect**; bundle ID is **`com.resurrect`**.
- Log file is `~/Library/Logs/resurrect.log` (toolkit reports under `~/Library/Logs/Resurrect/`).
- Settings migrate automatically from prior versions.

### Upgrade from CloudUnhideWatcher

1. Download `Resurrect-1.5.0-macos.dmg` and drag **Resurrect** to Applications.
2. Remove `/Applications/CloudUnhideWatcher.app` if it is still present.
3. **Re-grant Full Disk Access** for **Resurrect** (macOS treats the new bundle ID as a different app).
4. **Re-enable Launch at Login** if you used it before.
5. Optionally remove leftover FDA entries for the old app name.

```bash
tccutil reset SystemPolicyAllFiles com.resurrect
```

Then enable **Resurrect** in System Settings → Privacy & Security → Full Disk Access.

### Documentation

- [Getting Started](https://github.com/roto31/Resurrect/blob/main/docs/getting-started.md)
- [Wiki](https://github.com/roto31/Resurrect/wiki)
