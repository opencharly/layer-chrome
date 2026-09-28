# chrome

Google Chrome browser layer for OpenCharly images — cross-distro, at a stable
binary path.

The `chrome` candy installs Google Chrome and lands the
`google-chrome-stable` binary plus its `.desktop` entry, then best-effort
registers it as the default web browser via `xdg-settings`. It is deliberately
scoped to the browser itself: no CDP scaffolding and no supervisord. For
headless container CDP automation, compose `chrome-cdp` on top.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `chrome` |
| Binary | `/usr/bin/google-chrome-stable` |
| Desktop entry | `/usr/share/applications/google-chrome.desktop` |
| Environment | `CHROME_FLAGS=--ozone-platform=wayland --enable-features=UseOzonePlatform,VaapiVideoDecodeLinuxGL,VaapiIgnoreDriverChecks` |
| Env accepts | `HTTP_PROXY`, `HTTPS_PROXY`, `NO_PROXY` |
| Volume | `chrome-data` → `~/.chrome-debug` |
| Resource caps | `shm_size: 1g`, `memory_max: 6g`, `memory_high: 5g`, `memory_swap_max: 2g` |
| Service / port | none (compose `chrome-cdp` for CDP on 9222 / MCP on 9224) |

Per-distro package sources:

- `fedora` — `google-chrome-stable` from Google's direct RPM repo (with
  `--setopt=tsflags=noscripts`), plus `vulkan-loader`, `mesa-dri-drivers`,
  `cups-libs`, `iproute`.
- `arch` — `google-chrome` from the AUR, plus `vulkan-icd-loader`, `mesa`,
  `libcups`, `iproute2`.

## How to use it

Compose the layer by pinning this repo in a box's `candy:` list:

```yaml
my-browser:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/layer-chrome:v2026.239.1623'
```

It is usually consumed through the `chrome-sway` or `sway-desktop` compositions
rather than directly. After the image is built:

```bash
ls -l /usr/bin/google-chrome-stable
ls -l /usr/share/applications/google-chrome.desktop
```

A `--version` invocation is not a reliable check here: when `chrome-cdp` wraps
the binary it launches a full browser instead of printing a version banner,
contending with a running desktop Chrome.

## Layout

- `charly.yml` — the `chrome:` candy entity: package arms, env, security caps,
  volume, and the default-browser + presence `plan:` checks, plus the embedded
  `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-selkies:chrome` — the browser layer, CDP proxy, and
  `chrome-wrapper` reference
- CDP / MCP: `/charly-check:cdp`, `/charly-selkies:chrome-devtools-mcp`
- Compositions: `/charly-selkies:chrome-sway`, `/charly-selkies:sway-desktop`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
