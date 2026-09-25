# Enter the Wired

## Install

### Full (ACCELA + SLSsteam + CloudRedirect)
```bash
curl -fsSL https://raw.githubusercontent.com/LucianoSkx/enter-the-wired/main/enter-the-wired | bash
```

### SLSsteam + Lua Plugins
```bash
curl -fsSL https://raw.githubusercontent.com/LucianoSkx/enter-the-wired/main/install-plugins | bash
```

## CloudRedirect

The full installer also sets up [CloudRedirect](https://github.com/Selectively11/CloudRedirect)
using the [cloudredirect-moon](https://github.com/swwayps/cloudredirect-moon) fork
(cross-distro attach fixes, legacy save-layout healing, worker-thread crash
containment), installed **natively** — no Flatpak:

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
- **swwayps** — cloudredirect-moon (Linux fixes used here)
- **Deadboy666** — Headcrab (h3adcr-b)
- **AceSLS** — SLSsteam
