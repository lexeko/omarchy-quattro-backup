# Example customization report

This is an abbreviated and fully synthetic example. A real report includes only
the components detected on that system.

```markdown
# Omarchy -> Quattro backup and customization guide

## Result

**BACKUP COMPLETE**

All requested backup operations completed.

## Backup and customization summary

- User-editable config files differing from stock: **3**
- Explicit Omarchy custom files: **1**
- Full `~/.config` archive: **created**

A file difference does not prove a manual edit. If you do not recognize a
setting, skip it. The full backup still preserves the original file.

## Customizations by component

### `hypr`

**Customized managed files:** `bindings.conf`, `monitors.conf`

Saved copies: `reapply/.config/hypr/`
Diffs: `diffs/config/hypr/`

Reapply only the bindings and monitor settings you still want. Translate them
into Quattro's current configuration instead of replacing its defaults.

### `omarchy`

**Explicit custom files:** `themes/my-theme/colors.toml`

Saved copies: `reapply/.config/omarchy/`
```

The full report points to saved copies and exact diffs. Detailed inventories and
application state remain in the private backup without being presented as user
customizations.
