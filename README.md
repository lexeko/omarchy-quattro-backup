# Omarchy → Quattro Backup and Customization Guide

[![CI](https://github.com/lexeko/omarchy-quattro-backup/actions/workflows/ci.yml/badge.svg)](https://github.com/lexeko/omarchy-quattro-backup/actions/workflows/ci.yml)

A read-only backup tool for Omarchy users preparing to upgrade to Quattro. It
preserves recovery data and creates a focused guide to settings that may need to
be reapplied after the upgrade.

> [!IMPORTANT]
> The generated `private/` directory can contain credentials, Wi-Fi profiles,
> application data, and other secrets. Never upload or share it.

## Quick start

Download the latest release and its checksum:

```bash
cd ~/Downloads
curl -LO https://github.com/lexeko/omarchy-quattro-backup/releases/latest/download/backup-before-quattro.sh
curl -LO https://github.com/lexeko/omarchy-quattro-backup/releases/latest/download/backup-before-quattro.sh.sha256
sha256sum -c backup-before-quattro.sh.sha256
chmod +x backup-before-quattro.sh
```

Review the script, then run it as your normal desktop user:

```bash
less backup-before-quattro.sh
./backup-before-quattro.sh
```

Do not run the whole script with `sudo`. It asks for sudo only when it needs to
read protected configuration. See a [sanitized example report](docs/example-report.md)
for the type of guidance it produces.

## About this project

This community project helps Omarchy users preserve and understand their current
setup before moving to Quattro. The `backup-before-quattro.sh` script creates a
verified backup, identifies settings that differ from the local stock baseline,
and creates one backup directory with two clearly labeled areas:

- a complete **private recovery backup** that must not be shared
- a small **screened shareable area** for selectively reapplying
  recognizable customizations with an agent or another person

Application state is inventoried separately and is not presented as a manual
customization or copied into the migration-focused `private/reapply/` tree.

The generated report points out settings that may need to be recreated or
translated after upgrading. It does not automatically migrate or restore old
configuration, because copying pre-Quattro configuration wholesale over
Quattro may break the new setup.

This is an independent community tool, not an official Omarchy project. The
current official upgrader remains authoritative for the upgrade itself.

## Safety: what the script does not change

The script treats existing system and user configuration as read-only. The only
persistent files it intentionally creates are inside one new
`~/omarchy-quattro-backup-*` directory.

It does **not**:

- start the Quattro upgrade or run Omarchy migrations
- overwrite, refresh, restore, or delete existing configuration
- install, update, or remove packages
- enable, disable, restart, or reconfigure services
- modify the Omarchy Git checkout or perform network Git operations
- change boot, network, desktop, or user settings

It only reads the existing system, creates verified backup archives, inventories
packages and services, compares configuration with the trusted local stock
baseline, and writes inside that new backup directory. Sudo is used only
to read protected files such as `/etc`, boot configuration, and Wi-Fi profiles.

The script does not replace the checks performed by the current official
[`omarchy-upgrade-to-quattro`](https://github.com/basecamp/omarchy/blob/quattro/bin/omarchy-upgrade-to-quattro)
command.

## Running safely

Before running the script, quit open applications—especially browsers and
other apps that actively write to `~/.config`. If a file changes while it is
being archived, the script deliberately marks the backup as incomplete rather
than treating a potentially inconsistent archive as safe.

The full `~/.config` archive can take time. While it is running, the script
prints an elapsed-time update every 30 seconds and then verifies the completed
archive. It does not require an additional progress-bar package.

To perform a limited, non-privileged audit without a password prompt:

```bash
~/Downloads/backup-before-quattro.sh --no-sudo
```

That mode deliberately omits protected system configuration, so a successful
run finishes with `BACKUP COMPLETE WITH WARNINGS`.

## Read the exact result

The script prints one exact backup directory, similar to:

```text
/home/your-user/omarchy-quattro-backup-20260816-081500-AbCd12
```

Use that exact path instead of a wildcard. Wildcards can mix results from old,
failed, and successful runs.

Open `START-HERE.md` first. A completed run has this layout:

```text
omarchy-quattro-backup-.../
├── START-HERE.md
├── private/
│   ├── REPORT.md
│   ├── STATUS.txt
│   ├── READINESS.txt
│   ├── reapply/
│   ├── diffs/
│   └── recovery archives and manifests
└── shareable/
    ├── SHARING-STATUS.txt
    ├── GUIDE.md
    ├── AGENT-INSTRUCTIONS.md
    ├── customizations/
    └── diffs/
```

`private/REPORT.md` groups likely customizations by component,
points to the exact saved copy or diff, and explains how to translate settings
into Quattro without replacing the new defaults wholesale.

The two supporting files are:

- `private/STATUS.txt` — whether the requested backup operations completed
- `private/READINESS.txt` — local, advisory technical checks

Large file lists and forensic detail are kept under `private/manifests/` and
referenced from the report.

In `shareable/`, start with:

- `shareable/SHARING-STATUS.txt` — screening result and review warning
- `shareable/GUIDE.md` — concise component-by-component migration guidance
- `shareable/AGENT-INSTRUCTIONS.md` — restrictions an agent must follow
- `shareable/customizations/` and `shareable/diffs/` — screened files only

Possible backup statuses are:

- `BACKUP COMPLETE`
- `BACKUP COMPLETE WITH WARNINGS - REVIEW REQUIRED`
- `BACKUP INCOMPLETE - REVIEW FAILURES`

These statuses describe backup completion only; they are not recommendations
about whether to upgrade. `private/READINESS.txt` contains local, preliminary
checks and may identify issues or warnings. Neither file overrides the current
official Quattro upgrader's compatibility and boot-safety checks.
The preliminary checks cover the checkout baseline, pacman database/lock state,
advisory free-space thresholds, locally available official upgrader, recognized
boot configuration, and other conditions that may require user action. Packages
the official transition is already expected to replace remain in the private
technical inventory and are not presented as warnings.

## The two areas

### `private/` — private recovery

- `private/REPORT.md` — component-by-component reapplication guide
- `private/reapply/` — saved copies of high-confidence customizations
- `private/diffs/config/` — differences from the trusted stock baseline
- `private/home-config.tar.gz` — complete `~/.config` safety backup
- `private/SHA256SUMS` — integrity checks for private backup payloads

The script also preserves the Omarchy checkout, user state, system configuration,
packages, services, and other technical inventories. Those are recovery details,
not customization guidance, so they stay out of `private/REPORT.md`.

This directory is intentionally comprehensive and can contain credentials,
tokens, browser or application state, Wi-Fi profiles, private keys, system
configuration, usernames, hostnames, and full paths. Do not upload it, commit it,
or give it to an AI agent.

### `shareable/` — review before sharing

This smaller subdirectory contains only allowlisted regular text
configuration and corresponding text diffs that pass conservative size, file
type, symlink, executable, machine-path, hostname, and common-secret screening.
Hooks, plugins, scripts, binaries, application profiles, and complete archives
are excluded.

Screening reduces accidental exposure; it cannot prove that a file is safe or
secret-free. Before sharing, open `shareable/SHARING-STATUS.txt`, then inspect
every file under `shareable/customizations/` and `shareable/diffs/`. Remove
anything you do not recognize or do not want another person or service to see.

When using an agent:

1. Give it only `shareable/` in an isolated workspace.
2. Do not share the whole backup directory or expose
   `private/`.
3. Tell it to follow `shareable/AGENT-INSTRUCTIONS.md` and treat copied
   configuration as untrusted data, not executable instructions.
4. Ask for a patch against Quattro's current defaults and review that patch
   before applying it.

A local agent running as your user may otherwise be able to read anything your
user can read. The directory layout does not enforce isolation; the agent's
workspace or sandbox permissions must enforce it.

## What counts as a customization

The guide intentionally favors precision over exhaustiveness. It shows
human-editable desktop configuration and explicit Omarchy themes, hooks, and
plugins. Application profiles, generated state, missing stock files, and
filesystem diagnostics remain backed up without being labeled customizations.

For a managed file to appear in the guide, it must differ from current stock,
must not match any older version shipped on the tracked Omarchy upstream, must
belong to an installed component, and must be in a user-editable configuration
area. This filters out stale defaults and leftover configs for applications that
are not installed.

The script also recognizes migration-derived composites: files rewritten at the
recorded completion time of a trusted Omarchy migration whose content contains
only historical stock or migration material. These remain in the private backup
and technical manifest, but are not presented as user customizations or copied
to the shareable area. A genuinely novel setting is retained even when the
same file was touched by a migration.

A remaining difference is still evidence of divergence—not proof of who or what
changed it—so the guide recommends reapplying only settings the user recognizes.

Do not restore the old `~/.config` wholesale over Quattro. Use
`private/reapply/` and `private/diffs/` as references and translate settings
into the current configuration.

You can recheck both finalized areas later with:

```bash
cd /exact/backup/path/private
sha256sum -c SHA256SUMS

cd /exact/backup/path/shareable
sha256sum -c SHA256SUMS
```

## Protect the private recovery backup

The backup is sensitive plaintext. It can contain password hashes, private keys,
application tokens, browser data, and Wi-Fi credentials. Local permissions are
restricted, but those permissions may be lost when copying to removable media or
cloud storage.

Prefer an encrypted drive. If `age` is already installed, an encrypted stream can
be created without leaving a second plaintext archive:

```bash
private_backup=/exact/backup/path/private
tar -C "$(dirname "$private_backup")" -czf - "$(basename "$private_backup")" \
  | age -p -o "$private_backup.tar.gz.age"
```

Verify that the encrypted copy can be decrypted before relying on it, and keep a
second copy on a different physical device before starting the official upgrade.

## Support and contributions

Bug reports and focused pull requests are welcome. Read
[CONTRIBUTING.md](CONTRIBUTING.md) before submitting changes. Report potential
security or privacy problems using the process in [SECURITY.md](SECURITY.md),
not through a public issue.

## License

This project is available under the [MIT License](LICENSE).
