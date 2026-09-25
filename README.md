# Enter the Wired

Everything this installer needs is hosted **in this repository**: scripts,
vendored assets (`vendor/`) and release assets (`latest` release here).
No third-party hosts are required.

## Install

### Full (ACCELA + SLSsteam + CloudRedirect)
```bash
curl -fsSL https://raw.githubusercontent.com/LucianoSkx/enter-the-wired/main/enter-the-wired | bash
```

### SLSsteam + Lua Plugins
```bash
curl -fsSL https://raw.githubusercontent.com/LucianoSkx/enter-the-wired/main/install-plugins | bash
```

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
| `vendor/cloudredirect/` | Vendored CloudRedirect stack: `.so`, CLI, Qt6 GUI source, icons, `steam.sh` loaders, stock `client.sh` |

## CloudRedirect

Installed **natively** — no Flatpak — from the vendored
[cloudredirect-moon](https://github.com/swwayps/cloudredirect-moon) stack:

- `~/.local/share/CloudRedirect/cloud_redirect.so` — 32-bit hook, loaded into
  Steam via `LD_PRELOAD` by the patched `steam.sh`
- `~/.local/share/CloudRedirect/cloud-redirect-ui` — native Qt6 GUI
- **CloudRedirect** entry in your app launcher (`.desktop`) to sign in to your
  cloud provider (Google Drive, OneDrive, S3, local folder, ...)
- `DisableCloud: no` set in the SLSsteam config so cloud saves are exposed

Repair, update or rebuild it any time:

```bash
~/enter-the-wired/cloudredirect            # reinstall / repair
~/enter-the-wired/cloudredirect --rebuild  # force GUI rebuild
~/enter-the-wired/cloudredirect --no-ui    # .so + config only
```

Requirements: SLSsteam already installed (run the full installer first) and
`qt6-base` + `qt6-declarative` for the GUI (the script installs them when
possible). Then open the GUI, sign in to your provider, add the games under
`AdditionalApps` in `~/.config/SLSsteam/config.yaml` and restart Steam.

## Credits

- **ciscosweater** — Enter the Wired (original installer)
- **Selectively11** — CloudRedirect upstream
- **swwayps** — cloudredirect-moon (Linux fixes vendored here)
- **Deadboy666** — Headcrab (h3adcr-b), vendored
- **AceSLS** — SLSsteam
