# GlazePKG

Eye-candy TUI package viewer for all your package managers. Tokyo Night theme, 13 managers, fuzzy search, live descriptions, snapshots, and diffs.

![demo](demo.gif)

## Install

```bash
go install github.com/neur0map/glazepkg/cmd/gpk@latest
```

If `gpk` is not found after installing, add Go's bin directory to your PATH:

```bash
# bash (~/.bashrc) or zsh (~/.zshrc)
echo 'export PATH="$PATH:$HOME/go/bin"' >> ~/.bashrc
source ~/.bashrc
```

```fish
# fish
fish_add_path ~/go/bin
```

Or build from source:

```bash
git clone https://github.com/neur0map/glazepkg.git
cd glazepkg
go build ./cmd/gpk
```

## Update

```bash
gpk update
```

Self-updates the binary to the latest release. Run `gpk version` to check your current version.

## Usage

```
gpk              Launch TUI
gpk update       Self-update to latest release
gpk version      Show current version
gpk --help       Show keybind reference
```

Just run `gpk` — it drops straight into a beautiful table.

## Keybinds

| Key | Action |
|---|---|
| `j`/`k`, `↑`/`↓` | Navigate up/down |
| `g`/`G` | Jump to top/bottom |
| `Ctrl+d`/`Ctrl+u` | Half page down/up |
| `PgDn`/`PgUp` | Page down/up |
| `Tab`/`Shift+Tab` | Cycle manager tabs |
| `/` | Fuzzy search |
| `Esc` | Clear search / close overlay |
| `Enter` | Package details |
| `e` (detail) | Edit description |
| `r` | Rescan all managers |
| `s` | Save snapshot |
| `d` | Diff against last snapshot |
| `e` | Export packages |
| `?` | Toggle help overlay |
| `q` | Quit |

## Supported Package Managers

| Manager | Detection | What it scans | Descriptions |
|---|---|---|---|
| brew | `brew info --json=v2 --installed` | Explicitly requested formulae | batch via JSON |
| brew-deps | (same scan) | Auto-installed brew dependencies | batch via JSON |
| pacman | `pacman -Qen` | Explicit native packages | `pacman -Qi` |
| AUR | `pacman -Qm` | Foreign/AUR packages | `pacman -Qi` |
| apt | `dpkg-query -W` | Debian/Ubuntu packages | `apt-cache show` |
| dnf | `dnf list installed` | Fedora/RHEL packages | `dnf info` |
| snap | `snap list` | Snap packages | `snap info` |
| pip | `pip list --not-required` | Top-level Python packages | `pip show` |
| pipx | `pipx list --json` | Isolated Python CLI tools | — |
| cargo | `cargo install --list` | Rust binaries | — |
| go | `~/go/bin/` | Go binaries | — |
| npm | `npm list -g --depth=0` | Global Node packages | `npm info` |
| bun | `bun pm ls -g` | Global Bun packages | — |
| flatpak | `flatpak list --app` | Flatpak apps | `flatpak info` |

- Managers that aren't installed are silently skipped.
- Brew separates explicitly installed formulae from auto-pulled dependencies — deps go in a dedicated **deps** tab showing which tool required them.
- Descriptions are fetched in the background and cached for 24 hours.
- Packages with available updates show a `↑` indicator next to their version (checked every 7 days).
- You can add custom descriptions to any package by pressing `e` in the detail view — these persist across sessions and won't be overwritten by fetched descriptions.

## Data Storage

| Data | Path | Retention |
|---|---|---|
| Scan cache | `~/.local/share/glazepkg/cache/scan.json` | 10 days |
| Description cache | `~/.local/share/glazepkg/cache/descriptions.json` | 24 hours |
| Update cache | `~/.local/share/glazepkg/cache/updates.json` | 7 days |
| User notes | `~/.local/share/glazepkg/notes.json` | permanent |
| Snapshots | `~/.local/share/glazepkg/snapshots/` | permanent |
| Exports | `~/.local/share/glazepkg/exports/` | permanent |

Scan results are cached so `gpk` opens instantly. After 10 days it rescans automatically. Press `r` to force a rescan anytime.

## License

MIT
