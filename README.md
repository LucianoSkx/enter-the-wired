# Enter the Wired

Self-hosted combo installer for Steam on Linux — **ACCELA + SLSsteam + CloudRedirect**
installed from a single pipe. Every asset (scripts, vendored code, release binaries)
is hosted **in this repository**: no third-party hosts, no Flatpak.

## Install

### Full (ACCELA + SLSsteam + CloudRedirect)

```bash
curl -fsSL https://raw.githubusercontent.com/LucianoSkx/enter-the-wired/main/enter-the-wired | bash
```

### SLSsteam + Lua plugins only

```bash
curl -fsSL https://raw.githubusercontent.com/LucianoSkx/enter-the-wired/main/install-plugins | bash
```

## What gets installed

| Component | Where | What it does |
|---|---|---|
| ACCELA | `~/.local/share/ACCELA` | ACCELA runtime |
| SLSsteam | `~/.local/share/SLSsteam` | Patched Steam Linux Runtime: Lua plugins, `AdditionalApps` injection, cloud plumbing |
| CloudRedirect | `~/.local/share/CloudRedirect` | Native Steam Cloud → your own cloud provider (32-bit hook + Qt6 GUI) |

The installer patches `steam.sh`, which loads both stacks on every Steam start and
shows a **`Start SLSsteam`** notification when they are ready:

```
Start SLSsteam — SLSsteam + CloudRedirect loaded (client <version>)
```

Everything is copied to `~/enter-the-wired/` for later use (repair, update, uninstall):

```bash
~/enter-the-wired/accela        # install/upgrade ACCELA
~/enter-the-wired/slssteam      # rerun the SLSsteam/Headcrab installer
~/enter-the-wired/cloudredirect # install/repair CloudRedirect
~/enter-the-wired/fix-deps      # fix missing system dependencies
~/enter-the-wired/uninstall     # remove everything
```

## CloudRedirect setup

CloudRedirect is installed **natively** from the vendored
[cloudredirect-moon](https://github.com/swwayps/cloudredirect-moon) stack:

- `~/.local/share/CloudRedirect/cloud_redirect.so` — 32-bit hook, loaded into
  Steam by the patched `steam.sh`
- `~/.local/share/CloudRedirect/cloud-redirect-ui` — native Qt6 GUI
- **CloudRedirect** launcher entry (`.desktop`) to sign in to your provider
- `DisableCloud: no` in `~/.config/SLSsteam/config.yaml` (set automatically)

First-time setup:

1. Open the **CloudRedirect** app from your launcher and sign in
   (Google Drive, OneDrive, S3, local folder, ...)
2. Add the games you want to sync — AppIds and DLCs — under `AdditionalApps`
   in `~/.config/SLSsteam/config.yaml`
3. Restart Steam

Repair, update or rebuild any time:

```bash
~/enter-the-wired/cloudredirect            # reinstall / repair
~/enter-the-wired/cloudredirect --rebuild  # force GUI rebuild
~/enter-the-wired/cloudredirect --no-ui    # .so + config only
```

Requirements: SLSsteam installed first (run the full installer) and `qt6-base` +
`qt6-declarative` for the GUI (the script installs them when possible).

### How syncing works

- The hook intercepts Steam Cloud calls inside Steam and redirects them to your
  provider — your saves land in `CloudRedirect/<account>/<appid>/blobs/`.
- Uploads happen **when you save and close the game** (session barrier); nothing
  is pushed while it is open.
- Games without Steam Cloud support have nothing to sync — this is not a generic
  backup tool.
- Stats/achievements sync too (`stats.json`), driven by the GUI toggles.

## Troubleshooting

**Saves are not uploading**
Play, save, close the game, then check the log:

```bash
grep -E "Uploaded|rule path missing" ~/.config/CloudRedirect/cloud_redirect.log | tail
```

Look for `Uploaded .../blobs/...` after quitting. If you see `rule path missing`,
see below.

**`rule path missing` — Proton prefix on a secondary Steam library**
If your games live in another library (e.g. `/mnt/games/SteamLibrary`), the hook
only resolves prefixes under the main library. The installer fixes this: every
run of `~/enter-the-wired/slssteam` reads `libraryfolders.vdf` and symlinks any
missing `compatdata/` prefixes into the main library. Re-run it after installing
a game into a secondary library.

**`rule path missing` — folder named after a different SteamId**
SLSsteam spoofing the SteamId an app sees: the game saves under the spoofed
SteamId folder while the rule expands `{64BitSteamID}` to your real one. Symlink
them inside the game's save directory:

```bash
ln -s <folder-game-saved-in> <folder-rule-expects>
```

**GUI not building**
Install Qt6 (`qt6-base`, `qt6-declarative`) and run
`~/enter-the-wired/cloudredirect --rebuild`.

## Repository layout

| Path | Contents |
|---|---|
| `enter-the-wired` | Full combo installer (ACCELA + Headcrab + CloudRedirect) |
| `accela` | ACCELA installer (uses this repo's `deps.tar.gz` release asset) |
| `install-plugins` | Lua plugins + plugins app (uses this repo's release assets) |
| `cloudredirect` | Native CloudRedirect installer (uses `vendor/cloudredirect/`) |
| `fix-deps` | System dependency repair |
| `uninstall` | Removes everything |
| `vendor/headcrab.sh` | Vendored Headcrab (SLSsteam) installer |
| `vendor/headcrab.desktop` | Headcrab Updater launcher entry (points at this fork) |
| `vendor/cloudredirect/` | Vendored CloudRedirect stack: `.so`, CLI, Qt6 GUI source, icons, `steam.sh` loaders, stock `client.sh` |
| `flake.nix` | Nix dev shell (`nix develop`) |

## Uninstall

```bash
~/enter-the-wired/uninstall
```

## Credits

- **ciscosweater** — Enter the Wired (original installer, MIT)
- **Selectively11** — CloudRedirect upstream
- **swwayps** — cloudredirect-moon (Linux fixes vendored here)
- **Deadboy666** — Headcrab (h3adcr-b), vendored
- **AceSLS** — SLSsteam
