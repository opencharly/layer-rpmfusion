# rpmfusion

Enables the [RPM Fusion](https://rpmfusion.org) free and nonfree repositories on
Fedora-based OpenCharly images.

The `rpmfusion` candy installs the `rpmfusion-free-release` and
`rpmfusion-nonfree-release` packages from `download.rpmfusion.org` with their
OpenPGP signatures **verified**, by importing the RPM Fusion keys from Fedora's
own signed `distribution-gpg-keys` package first. Each release package registers
its repo definitions under `/etc/yum.repos.d/` and drops the repo signing-key
files under `/etc/pki/rpm-gpg/`.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `rpmfusion` |
| Repos | `rpmfusion-free`, `rpmfusion-nonfree` |
| Repo files | `/etc/yum.repos.d/rpmfusion-{free,nonfree}.repo` |
| Key import | `distribution-gpg-keys` → `rpmkeys --import` |
| Distros | Fedora |

## How to use it

Compose the layer by pinning this repo in a box's `candy:` list:

```yaml
my-nonfree-box:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/layer-rpmfusion:v2026.239.1634'
```

The candy's `plan:` asserts the signing key is in the rpm database, both repo
files are on disk, both release packages are recorded, and the free repo file
declares its `[rpmfusion-free]` section.

## Why the key import is a separate step

The release packages are fetched as URLs, which `dnf` treats as the
`@commandline` repo and — with default settings — installs **without** an
OpenPGP check. The RPM Fusion keys are distributed only inside those very
packages, so extracting the key from the unverified rpm would be circular. The
non-circular trust chain (documented at `rpmfusion.org/keys`) is Fedora's own
signed `distribution-gpg-keys`, which carries the RPM Fusion keys; importing from
there lets `localpkg_gpgcheck=1` verify the release rpms for real.

## Layout

- `charly.yml` — the `rpmfusion:` candy entity (the key-import + verified-install
  command step and the `check:` probes) and the embedded `rpmfusion-skill:`
  skill entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-distros:rpmfusion`
- Consumers: `/charly-distros:fedora-nonfree`, `/charly-distros:fedora-builder`
- Codecs: `/charly-selkies:ffmpeg`, `/charly-immich:immich`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
