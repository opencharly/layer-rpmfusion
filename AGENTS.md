# AGENTS.md — layer-rpmfusion

Standalone candy repo for the `rpmfusion` layer — the RPM Fusion free and nonfree
Fedora repository configuration. The candy lives in `charly.yml` at the repo
root: the key-import + verified-install command step, the `check:` probes, and
the embedded `skill:` entity projected into the marketplace corpus as
`/charly-distros:rpmfusion`.

Canonical files:

- `charly.yml` — the `rpmfusion:` candy entity and the `rpmfusion-skill:` skill
  entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-distros:rpmfusion` — the owning skill. The repo setup, the key-import
  trust chain, and the consumers (`fedora-nonfree`, `fedora-builder`). Load
  before editing or troubleshooting the candy.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `command:`/`check:`). Load before editing any entity
  field or plan step.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- `charly box validate` at the repo root checks the manifest parses and
  validates.
- The candy's `plan:` `check:` steps are the functional evidence: the RPM Fusion
  signing key in the rpm database (proving the release packages were verified,
  not trusted blind), both repo files on disk, both release packages recorded,
  and the `[rpmfusion-free]` section header.
- Do not weaken the trust chain: the key import from `distribution-gpg-keys` and
  the `localpkg_gpgcheck=1` install are what remove the "skipped OpenPGP checks"
  warning. `--nogpgcheck` is not an acceptable substitute.

## Modify this repo

- Edit the `rpmfusion:` candy entity AND the `rpmfusion-skill:` skill entity in
  `charly.yml` together. The skill is the projected usage source, so a repo or
  trust-chain change not mirrored in the skill leaves the corpus stale.
- New behaviour claims belong in the `plan:` as an observable `check:` step, and
  in the skill body.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
