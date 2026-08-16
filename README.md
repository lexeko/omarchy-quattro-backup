# Omarchy to Quattro Backup

[![CI](https://github.com/lexeko/omarchy-quattro-backup/actions/workflows/ci.yml/badge.svg)](https://github.com/lexeko/omarchy-quattro-backup/actions/workflows/ci.yml)

This tool saves your current Omarchy setup before you upgrade to Quattro.

It puts your config changes and diffs in one place. You can quickly see what
changed. You can then decide what to reapply after the upgrade.

The script only reads your existing setup. It does not upgrade Omarchy or change
your current configuration.

## What you get

The script creates one backup folder in your home directory.

It contains:

- a full backup of `~/.config`
- copies of likely custom settings
- diffs against the stock Omarchy config
- a short guide grouped by app or component
- package, service, system, and Omarchy details for recovery

Open `START-HERE.md` when the backup finishes. Then open
`private/REPORT.md`. It shows the settings that may need to be reapplied.

See this [sanitized example report](docs/example-report.md).

## Download and run

Download the latest release and verify it:

```bash
cd ~/Downloads
curl -LO https://github.com/lexeko/omarchy-quattro-backup/releases/latest/download/backup-before-quattro.sh
curl -LO https://github.com/lexeko/omarchy-quattro-backup/releases/latest/download/backup-before-quattro.sh.sha256
sha256sum -c backup-before-quattro.sh.sha256
chmod +x backup-before-quattro.sh
```

Review the script:

```bash
less backup-before-quattro.sh
```

Run it as your normal desktop user:

```bash
./backup-before-quattro.sh
```

Do not run the whole script with `sudo`. It asks for sudo when it needs to read
protected system files.

## Close apps first

Quit open apps before you run the script. Browsers and other apps often update
files in `~/.config` while they are open.

A file may change while it is being copied. The script treats that as an
incomplete backup. Closing apps also reduces socket warnings and can make the
backup faster.

The `~/.config` archive can take several minutes. The script prints an update
every 30 seconds while it works.

## Read the result

The script prints the exact path to the new backup folder. It looks like this:

```text
/home/your-user/omarchy-quattro-backup-20260816-081500-AbCd12
```

The main files are:

- `START-HERE.md`: where to begin
- `private/REPORT.md`: config changes and steps to reapply them
- `private/STATUS.txt`: whether the backup completed
- `private/READINESS.txt`: extra technical checks
- `private/reapply/`: saved copies of likely custom settings
- `private/diffs/`: config diffs against stock Omarchy
- `shareable/GUIDE.md`: a screened guide that you can review before sharing

The final status describes the backup. It does not tell you whether you should
upgrade.

## Reapply settings after Quattro

Let the official Quattro upgrade create its new defaults first.

Then work through `private/REPORT.md` one component at a time. Review each diff.
Keep only the settings you recognize and still want.

Do not copy your old `~/.config` over Quattro. Quattro uses new config formats
for parts of the desktop. Translate the settings into the new config instead.

The official `omarchy-upgrade-to-quattro` command remains the source of truth
for the upgrade itself.

## What the script does not do

The script does not:

- start the Quattro upgrade
- overwrite or delete your existing config
- install, update, or remove packages
- change services
- change boot, network, or desktop settings
- change the Omarchy source checkout

It writes only to its new backup folder.

Sudo is used only to read protected files. This includes `/etc`, boot config,
and Wi-Fi profiles.

You can skip protected files with:

```bash
./backup-before-quattro.sh --no-sudo
```

This creates a smaller backup. The result will show a warning because protected
system files were skipped.

## Keep the backup private

The `private/` folder can contain secrets. It may include tokens, Wi-Fi
passwords, browser data, private keys, usernames, and full file paths.

Never upload or share `private/`. Do not give it to an AI agent.

The smaller `shareable/` folder contains screened config files and diffs. The
screening is not a guarantee. Read `shareable/SHARING-STATUS.txt`. Then inspect
every file before sharing it.

If you use an agent, give it only `shareable/` in an isolated workspace. Ask it
to follow `shareable/AGENT-INSTRUCTIONS.md`. Review every proposed change before
you apply it.

## How config changes are selected

The script compares your config with the local stock Omarchy baseline. It keeps
likely user changes in the guide. It filters out old stock files, migration
changes, inactive apps, and generated application state when it can identify
them.

A difference does not prove that you changed a file yourself. Skip settings you
do not recognize. The full backup still preserves the file.

## Check the backup later

You can verify both parts of the backup:

```bash
cd /exact/backup/path/private
sha256sum -c SHA256SUMS

cd /exact/backup/path/shareable
sha256sum -c SHA256SUMS
```

Keep another copy on a different physical device before upgrading. Use an
encrypted drive when possible.

If `age` is installed, you can encrypt the private backup:

```bash
private_backup=/exact/backup/path/private
tar -C "$(dirname "$private_backup")" -czf - "$(basename "$private_backup")" \
  | age -p -o "$private_backup.tar.gz.age"
```

Test that you can decrypt the encrypted copy before relying on it.

## About this project

This is an independent community tool. It is not an official Omarchy project.

Bug reports and focused pull requests are welcome.

Do not attach real backups or private config files to GitHub issues.

This project uses the [MIT License](LICENSE).
