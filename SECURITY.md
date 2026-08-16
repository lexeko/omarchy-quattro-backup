# Security Policy

## Supported version

Security and privacy fixes are made against the latest release.

## Reporting a problem

Use GitHub's private vulnerability reporting feature from the repository's
Security tab. Please do not open a public issue for a suspected vulnerability,
secret-screening bypass, unsafe archive behavior, or unintended system change.

Include:

- the affected release or commit
- a concise description of the impact
- safe reproduction steps using synthetic data
- any suggested mitigation

Do not send real backup archives, credentials, Wi-Fi profiles, private keys, or
personal configuration. A maintainer will respond through the private report.

## Security boundary

The `shareable/` screening is defensive assistance, not a guarantee that files
are free of secrets or personal information. Users must manually review every
file before sharing it. The complete `private/` directory must never be shared.
