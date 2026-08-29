# Process Flows

Visual reference for how **Resurrect** watches folders, restores hidden files, and how **iCloud Tools** diagnoses and resolves sync issues.

## Application lifecycle

```mermaid
flowchart LR
  Launch[Launch app] --> FDA{CloudDocs accessible?}
  FDA -->|no| Prompt[FDA alert + menu warning]
  FDA -->|yes| Watch[Start FSEventStream]
  Watch --> Idle[Menu: idle / watching]
  Idle --> Event[FSEvents under Desktop/Documents]
  Event --> Restore[Scan + restore hidden flags]
  Restore --> Idle
  Prompt --> FDA
```

## Hidden-file restore pipeline

When a scan runs (on FSEvents or **Scan Now**), each path is evaluated and cleared if eligible.

```mermaid
flowchart TD
  Start[Scan watch roots + children] --> RootClear[Clear hidden on root folders]
  RootClear --> Walk[Walk directory tree]
  Walk --> Check{Eligible?}
  Check -->|no| Skip[Skip dotfiles, placeholders, system names]
  Check -->|yes| Dual[Clear Finder isHidden + BSD UF_HIDDEN]
  Dual --> Log[Log restore count]
  Log --> Rehide{iCloud re-hides within 1s?}
  Rehide -->|yes| Pin[Pin watchdog: 1s re-clear up to 120s]
  Rehide -->|no| Done[Path stays visible]
  Pin --> Done
  Skip --> Walk
```

### Eligibility (what gets restored)

| Included | Excluded |
|----------|----------|
| Regular files and folders under watch roots | Names starting with `.` |
| User content in Desktop/Documents | `.icloud` placeholders |
| Watch roots themselves if hidden | `.DS_Store`, `.Trash`, etc. |

## Pin watchdog (re-hide fight)

iCloud sometimes re-applies `UF_HIDDEN` seconds after the app clears it. The pin watchdog keeps clearing contested paths until they stay visible.

```mermaid
sequenceDiagram
  participant App as Resurrect
  participant FS as Filesystem
  participant iCloud as iCloud sync agent
  App->>FS: Clear hidden on path
  iCloud->>FS: Re-apply UF_HIDDEN
  App->>FS: Pin: clear every 1s
  loop Up to 120s
    App->>FS: Re-clear if hidden
  end
  App->>App: Release pin after 3 clean checks
```

## Full Disk Access flow

```mermaid
flowchart TD
  Start[App launch] --> Probe[PermissionProbe on CloudDocs paths]
  Probe --> OK{Read/write OK?}
  OK -->|yes| Run[Start monitoring]
  OK -->|no| UI[Menu: Needs FDA + one-time alert]
  UI --> Open[Open FDA settings / Reveal app in Finder]
  Open --> Grant[User enables Resurrect in FDA]
  Grant --> Relaunch[Quit and relaunch]
  Relaunch --> Probe
```

Install from `/Applications/Resurrect.app` so FDA lists the correct app name (not `swift`).

## Operator troubleshooting flow

Use this when files keep disappearing from Finder or re-hide repeatedly.

```mermaid
flowchart TD
  Symptom[Files hidden or re-hiding] --> Diagnose[iCloud Tools: Diagnose or Full Report]
  Diagnose --> Conflicts{Conflict folders found?}
  Conflicts -->|no| PinOrSync[Check logs; likely iCloud materialization — not app bug]
  Conflicts -->|yes| Scan[Scan Conflict Folders dry run]
  Scan --> Unique{Mostly UNIQUE?}
  Unique -->|yes| Apply[Apply — move UNIQUE only]
  Unique -->|no| Differs{DIFFERS rows?}
  Differs -->|yes| Resolve[Resolve Conflicting Files interactive]
  Differs -->|no| Manual[Review IDENTICAL; manual cleanup later]
  Apply --> Verify[Verify after 1–2 days]
  Verify --> Regrown{REGROWN?}
  Regrown -->|yes| OtherMac[Repeat on each Mac on same iCloud account]
  Regrown -->|no| Done[Conflict flow complete]
```

## iCloud Tools command flow

```mermaid
flowchart LR
  subgraph readOnly [Read-only]
    D[diagnose]
    R[report = diagnose + scan]
    S[scan]
  end
  subgraph modify [Changes files]
    A[apply UNIQUE only]
    V[verify vs snapshot]
    X[resolve DIFFERS interactive]
  end
  D --> S
  S --> A
  A --> V
  S --> X
```

| Step | Command | Modifies disk? |
|------|---------|----------------|
| Health check | `diagnose` | No |
| Full picture | `report` | No |
| Preview merge | `scan` | No |
| Move UNIQUE files | `apply` | Yes (copy/move UNIQUE only) |
| Check regrowth | `verify` | No |
| Fix name collisions | `resolve` | Yes (with backup) |

## What the app does vs what you must fix

```mermaid
flowchart TB
  subgraph app [Resurrect handles]
    H[Clear local hidden flags — iCloud + optional local paths]
    W[Watch and re-clear pin paths]
    T[Run iCloud diagnose / merge toolkit]
    L[Run local hidden toolkit — opt-in]
  end
  subgraph user [Operator / iCloud fixes]
    M[Merge conflict folders across Macs]
    I[iCloud sync / materialization issues]
    F[FDA and correct install path]
    LH[Edit .hidden manifests manually]
  end
  H --> Visible[Files visible in Finder]
  L --> Visible
  M --> Visible
  I --> Visible
```

## Local Hidden File Assist (v1.3.0+)

Optional feature for **non-iCloud** folders. Off by default. See [Local hidden files](local-hidden-files.md) for full reference.

### Activation and runtime merge

```mermaid
flowchart TD
  Toggle[Settings: Assist with local hidden files ON] --> Menu[Local Tools menu visible]
  Toggle --> Paths[Add folders under $HOME]
  Paths --> Cont{Continuous watch ON?}
  Cont -->|yes| Merge[mergedWatchPaths = iCloud + local]
  Cont -->|no| iOnly[iCloud FSEvents only]
  Merge --> Monitor[FSEvents on iCloud + local paths]
  Monitor --> Scan[runScan all configured directories]
  Toggle --> Toolkit[Local Tools menu → local_hidden_toolkit.sh]
```

### Local toolkit vs in-app restore

```mermaid
flowchart LR
  subgraph menu [Menu Local Tools]
    D[diagnose]
    S[scan]
    A[apply]
    R[report]
  end
  subgraph swift [App runtime when continuous watch ON]
    FS[FSEvents]
    HR[HiddenFileRestorer.scan]
  end
  D --> Shell[local_hidden_toolkit.sh]
  S --> Shell
  A --> Shell
  R --> Shell
  FS --> HR
  Shell --> Clear[chflags + SetFile]
  HR --> Clear2[resourceValues + chflags + pin]
```

| Path | Trigger | Engine |
|------|---------|--------|
| Background restore | FSEvents or **Scan Now** | Swift `HiddenFileRestorer` (with pin watchdog) |
| Menu toolkit | **Local Tools** items | Bash `local_hidden_toolkit.sh` |
| One-off folder | No saved paths → folder picker | Toolkit with `--paths-file` manifest |

### Local troubleshooting flow

```mermaid
flowchart TD
  Symptom[Files hidden outside Desktop/Documents] --> Enable[Settings → Assist with local hidden files]
  Enable --> Add[Add Folder…]
  Add --> Diag[Local Tools → Diagnose or Full Local Report]
  Diag --> Hidden{Eligible hidden items?}
  Hidden -->|yes| Scan[Scan Local Folders dry run]
  Scan --> Apply[Apply Local Restore]
  Hidden -->|no| Dotfile{Dotfiles or .hidden manifest?}
  Dotfile -->|yes| Manual[Manual fix — out of scope]
  Dotfile -->|no| Done[No action needed]
  Apply --> Watch{Need ongoing watch?}
  Watch -->|yes| Cont[Enable Continuously monitor]
  Watch -->|no| Done
```

## Related

- [Architecture](architecture.md)
- [Local hidden files](local-hidden-files.md)
- [iCloud Tools](icloud-tools.md)
- [Troubleshooting](troubleshooting.md)
- [Getting Started](getting-started.md)
