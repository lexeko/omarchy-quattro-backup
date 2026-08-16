# Contributing

Thanks for helping improve this project.

## Before opening an issue

- Check existing issues first.
- Explain what you expected and what happened.
- Include the script version and relevant error text.
- Remove usernames, hostnames, paths, tokens, Wi-Fi details, and other private
  information before sharing output.
- Never attach a generated backup directory.

Report security or privacy problems through the process in
[SECURITY.md](SECURITY.md), not through a public issue.

## Making a change

Keep changes focused and preserve the core safety rule: the script may read
existing system state, but it must only write inside its newly created backup
directory.

Use Bash 5 syntax and quote paths that may contain spaces. Avoid adding runtime
dependencies unless they provide a clear benefit.

Run these checks before opening a pull request:

```bash
bash -n backup-before-quattro.sh
bash -n tests/run.sh
shellcheck backup-before-quattro.sh tests/run.sh
tests/run.sh
```

Add or update tests when behavior changes. Test data must be synthetic and must
not contain real credentials or personal configuration.
