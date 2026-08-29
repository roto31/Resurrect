# iCloud Tools

CloudUnhideWatcher bundles two maintenance scripts inside the app:

```
/Applications/CloudUnhideWatcher.app/Contents/Resources/Scripts/
├── diagnose_rehide.sh
└── merge_icloud_conflicts.sh
```

Run them from the menu (**iCloud Tools**) or from Terminal using the paths above. Reports are written to `~/Library/Logs/CloudUnhideWatcher/`.

## Diagnostic (read-only)

**Menu:** iCloud Tools → Run Diagnostic (read-only)

Checks:

- App install location
- Full Disk Access / CloudDocs path access
- Hidden watch roots (`~/Desktop`, `~/Documents`)
- Hidden files under watched trees
- iCloud conflict folders (`Desktop - hostname`, `Documents - hostname`)
- Session log contention (how often restores fire)

No files are modified.

## Conflict folder merge

When iCloud creates `Desktop - <hostname>` or `Documents - <hostname>` folders, files may need reconciliation into the live Desktop/Documents folders.

### Dry run (default)

**Menu:** iCloud Tools → Merge Conflicts (preview)

Lists each file as **UNIQUE**, **IDENTICAL**, or **DIFFERS**:

| Category | Meaning | Script action |
|----------|---------|---------------|
| **UNIQUE** | In conflict folder only | Moved with Apply |
| **IDENTICAL** | Same name, byte-for-byte match in live folder | Listed only |
| **DIFFERS** | Same name, different content | Manual review required |

### Apply

**Menu:** iCloud Tools → Merge Conflicts (apply UNIQUE only)

- Moves **UNIQUE** files into the live Desktop/Documents folder
- Saves a per-folder snapshot under `~/.cloudunhide_merge_state/` for verify
- Never overwrites existing live files
- Never deletes conflict-folder content automatically

### Verify

**Menu:** iCloud Tools → Merge Conflicts (verify snapshot)

Compares current conflict-folder contents to the last apply snapshot. **REGROWN** indicates ongoing sync conflict — address on all Macs signed into the same iCloud account.

## Terminal equivalents

```bash
SCRIPTS="/Applications/CloudUnhideWatcher.app/Contents/Resources/Scripts"

# Read-only diagnostic
bash "${SCRIPTS}/diagnose_rehide.sh"

# Preview merge
bash "${SCRIPTS}/merge_icloud_conflicts.sh"

# Apply UNIQUE files only
bash "${SCRIPTS}/merge_icloud_conflicts.sh" --apply

# Verify against last snapshot
bash "${SCRIPTS}/merge_icloud_conflicts.sh" --verify
```

## When to use

1. **Diagnose** first whenever files keep re-hiding.
2. **Preview** when diagnose reports conflict folders.
3. **Apply** when preview shows mostly UNIQUE files you want in live folders.
4. **Verify** days later to confirm conflict folders stopped growing.

## Related

- [Troubleshooting](troubleshooting.md)
- [Operator Guide](operator-guide.md)
