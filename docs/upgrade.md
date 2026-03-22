# Upgrade system

Pressing `u` in the package detail view triggers a manager-aware upgrade flow.

1. `handleDetailKey` catches the `u` key and calls `upgradeDetailPackage()`.
2. `upgradeDetailPackage` reads `m.detailPkg`, looks up the responsible manager via `manager.BySource`, and verifies the manager is available.
3. The manager is type asserted to the optional `manager.Upgrader` interface; only managers that implement it can receive commands.
4. `upgradeDetailPackage` builds the command via `UpgradeCmd(name)`, saves an `upgradeRequest` with the command string, and sets `confirmingUpgrade`. A centered overlay shows the exact command that will run, with selectable Yes/No buttons.
5. On confirmation the upgrade runs in a background goroutine. A notification appears in the bottom-right corner showing progress. The user can keep navigating the TUI.
6. When the command finishes, `upgradeResultMsg` delivers the result. Success triggers a rescan of the affected manager to refresh the package list. The notification shows success or the error message, then auto-dismisses.

## Confirmation

Every upgrade shows a confirmation overlay before running. The overlay displays:
- The package name and source manager
- The exact command that will be executed (including sudo prefix if applicable)
- A warning for privileged managers (apt, dnf, pacman, snap, apk, XBPS)
- Selectable Yes/No buttons navigable with arrow keys or Tab

This prevents accidental upgrades from a single keypress.

## Notification

The upgrade runs in the background while the TUI stays interactive. A small notification box in the bottom-right corner shows:
- A spinner and "upgrading <name>..." while the command is running
- A green checkmark on success, auto-dismisses after 5 seconds
- A red error message on failure, auto-dismisses after 8 seconds

## Supporting a new manager

Implement the standard `manager.Manager` methods (`Name`, `Available`, `Scan`). If your tool can upgrade single packages, implement `manager.Upgrader` by adding:

```go
func (m *YourManager) UpgradeCmd(name string) *exec.Cmd {
	return exec.Command("your-tool", "upgrade", name)
}
```

The UI will execute that command in a background goroutine. If the manager requires root, use `privilegedCmd` instead of `exec.Command` to auto-prefix with sudo on Unix. If your manager cannot upgrade packages individually, simply omit this method and `gpk` will show `manager.ErrUpgradeNotSupported`.

## Manager coverage

Managers with single-package upgrade support:

- `brew upgrade <name>`
- `sudo pacman -S <name>`
- `sudo apt install --only-upgrade -y <name>`
- `sudo dnf upgrade -y <name>`
- `sudo snap refresh <name>`
- `sudo apk add --upgrade <name>`
- `sudo xbps-install -S <name>`
- `pip install --upgrade <name>`
- `pipx upgrade <name>`
- `cargo install <name>`
- `npm install -g <name>@latest`
- `gem update <name>`
- `flatpak update -y <name>`
- `opam upgrade --yes <name>`
- `conda/mamba update --yes <name>`
- `luarocks upgrade <name>`
- `choco upgrade <name> --yes`
- `scoop update <name>`
- `winget upgrade <name>`
