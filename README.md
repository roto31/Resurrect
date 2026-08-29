# Resurrect

**Resurrect** is a macOS menu bar app that watches iCloud Drive Desktop & Documents and automatically clears the hidden flag from user files that iCloud has marked hidden — typically after a sync conflict or a failed/evicted-file (dataless) materialization.

**This is a mitigation for a symptom, not a fix for the underlying iCloud sync problem.** If files keep re-hiding, see [Troubleshooting](docs/troubleshooting.md).

# Why Resurrect Exists

I went looking for a file that I knew I'd saved. It wasn't on the Desktop. It wasn't in Documents. Spotlight had nothing. For a minute I did the normal thing — assumed I'd imagined saving it, or dragged it somewhere without noticing.

Then I remembered the shortcut every Mac user eventually learns: `Cmd + Shift + .` Show hidden files.

The file was right there. So were a dozen others I hadn't even clocked as missing.

They weren't lost. They were *hidden* — the BSD `UF_HIDDEN` flag set on files that had no business being invisible, sitting exactly where I'd left them, just switched off from view. I confirmed it in Terminal, cleared the flag with `chflags nohidden`, toggled `AppleShowAllFiles` for the rest, relaunched Finder. Everything came back. I closed the terminal and moved on.

A few days later, same thing. Some of the same files. Some new ones.

I ran the commands again. Worked again. Then it happened *again* — fast enough that it felt less like a glitch and more like something actively fighting me for the files. I've spent fifteen years doing Apple platform engineering at enterprise scale — Jamf deployments from 400 to 20,000+ endpoints, zero-downtime migrations, the whole résumé. This is the bug that had me running the same three terminal commands on a loop like a first-year help desk tech.

## Down the rabbit hole

I started reading. Apple's own support communities, MacRumors threads going back years, Reddit posts with hundreds of upvotes and no real answer in the replies — all describing the same thing. iCloud Drive quietly marking Desktop and Documents files as hidden, no warning, no explanation in the UI, no real fix from Apple beyond the usual troubleshooting shrug. It was "documented" in the sense that plenty of people had documented *experiencing* it. Nobody had documented *why*.

So I built a tool to fight it directly: a menu bar app that watched Desktop and Documents and cleared the hidden flag the moment it caught one being set. It worked — files stayed visible, as long as the app was running and paying attention.

But "as long as it's paying attention" was the tell. Every so often a file would get re-hidden faster than my watcher could hold the line — cleared one second, hidden again the next. That's not a bug you out-engineer by reacting faster. That's a bug where you're fighting the wrong layer of the problem.

## What was actually happening

The real cause wasn't on my Mac. It was iCloud trying, and failing, to reconcile Desktop and Documents across every Mac that had ever synced to that account. Every unresolved conflict left behind a folder — `Desktop - <hostname>`, `Documents - <hostname>` — another machine's unmerged snapshot of the same folder. Some belonged to Macs I use every day. One belonged to a machine that had been gone for over a decade — its conflict folder held files dated back to 2008.

None of that was inert. iCloud kept circling back to reconcile it, and that background churn was what kept re-triggering the hidden flag on files that had nothing to do with the conflict at all. I wasn't losing a race against randomness. I was losing a race against thousands of orphaned files left behind by machines that, in one case, hadn't existed for years.

Once I actually found the conflict folders, recovered what was genuinely unique in them, and sorted out the real duplicates from the real conflicts by hand, the re-hiding stopped being a mystery with no fix. It had a cause. Causes can be fixed.

## The name

The files were never gone. They were buried under years of unresolved sync history, pulled back into view over and over, only to disappear again — until the actual cause got dug out and dealt with instead of just patched around every time it resurfaced.

That's the name. **Resurrect** doesn't just unhide a file once. It watches, brings files back the moment iCloud tries to hide them again, and — paired with the diagnostic tooling in this repo — helps you find and fix the conflict actually sitting upstream, instead of running the same terminal command on a loop for the rest of your life.

If you're here because your own files keep hiding from you and `Cmd + Shift + .` only bought you a day: you're not imagining it, and you're not the only one. Keep reading.

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
