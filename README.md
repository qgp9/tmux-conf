[![Built with Claude Code](https://img.shields.io/badge/Built%20with-Claude%20Code-blue)](https://claude.ai/claude-code)

[한국어](README.ko.md)

# 2-Layer tmux Configuration

A nested 2-layer tmux setup where outer (base) and inner tmux run simultaneously with separate prefixes, avoiding key conflicts.

## File Structure

```
~/.config/tmux/
├── common.conf        # Shared settings (sourced by both layers)
├── tmux.conf          # Inner layer (prefix: C-a)
├── tmux_base.conf     # Outer/base layer (prefix: C-q)
└── tbase              # Script to launch base tmux
```

### Load Order

- **Base**: `common.conf` → `tmux_base.conf`
- **Inner**: `common.conf` → `tmux.conf` → TPM (plugins)

## Usage

```bash
# Launch base tmux
tbase

# Launch inner (wing) tmux inside base
# Must unset TMUX since tmux refuses nested execution when it's set
unset TMUX
tmux
```

> **Note**: When running wing on a remote host, or running standalone without base,
> the `TMUX` variable is not set, so you can simply run `tmux` directly.

## Layer Distinction

| | Inner (WING) | Base (BASE) |
|---|---|---|
| **Prefix** | `C-a` | `C-q` |
| **Status bar background** | Black | Grey (colour235) |
| **Tag** | `WING` (yellow bold) | `BASE` (red bold) |
| **Plugins** | TPM, sensible, tilish | None |

Status bar examples:

```
Inner: [WING ▸ myhost:session][M:On ]      0:zsh 1:vim*    [~/wrkp][title][2026-03-01 14:30]
Base:  [BASE ▸ myhost:session][M:Off]      0:zsh*           [~/wrkp][title][2026-03-01 14:30]
Sync:  [WING ▸ myhost:session][M:On ] SYNC 0:zsh 1:vim*    [~/wrkp][title][2026-03-01 14:30]
```

## Key Bindings

### Prefix

| Layer | Prefix | Last window | Prefix passthrough |
|---|---|---|---|
| Inner (WING) | `C-a` | `C-a C-a` | `C-a a` |
| Base (BASE) | `C-q` | `C-q C-q` | `C-q q` |

### Common (common.conf)

| Key | Action |
|---|---|
| `prefix + m` | Toggle mouse mode |
| `prefix + h/j/k/l` | Vim-style pane navigation |
| `prefix + H/J/K/L` | Vim-style pane resize (repeatable) |
| `prefix + s` | Toggle synchronize-panes (shows SYNC) |
| `prefix + Space` | Next window |
| `prefix + BSpace` | Previous window |
| `prefix + "` | Horizontal split (current path) |
| `prefix + %` | Vertical split (current path) |
| `prefix + c` | New window (current path) |
| `prefix + A` | Rename window |
| `prefix + r` | Reload config |

### VI Copy-mode

| Key | Action |
|---|---|
| `prefix + [` | Enter copy-mode |
| `v` | Begin visual selection |
| `y` | Copy selection |
| `yy` | Copy entire line |
| `Escape` | Exit copy-mode |

Copied text is automatically sent to the system clipboard via OSC52 (works over SSH as well).

### Inner Only

| Key | Action |
|---|---|
| `Alt + Enter` | Split at bottom-right pane |
| `Alt + Number` | Switch workspace (tilish) |

## Key Settings

- **Terminal**: `tmux-256color`
- **escape-time**: `0` (minimizes latency in 2-layer setup)
- **focus-events**: `on` (forwards focus events to inner tmux)
- **Clipboard**: OSC52 (requires `~/.config/tmux/copy-osc52.sh`)
- **Path display**: `$HOME` → `~` substitution (tmux built-in format, 3.1+)

## Installation

### TPM (Tmux Plugin Manager)

TPM is required for the inner (WING) layer to use plugins (sensible, tilish).

```bash
git clone https://github.com/tmux-plugins/tpm ~/.config/tmux/plugins/tpm
```

After installation, press `prefix + I` in inner tmux to install plugins.

### Plugins

| Plugin | Description |
|---|---|
| [tmux-sensible](https://github.com/tmux-plugins/tmux-sensible) | Sensible default settings for most users (utf8, status-keys, etc.) |
| [tmux-tilish](https://github.com/jabirali/tmux-tilish) | i3wm-style window management. `Alt+Number` for workspace switching, `Alt+Enter` for new panes |

## Requirements

- tmux 3.1+
- `~/.config/tmux/copy-osc52.sh` (clipboard integration)
- [TPM](https://github.com/tmux-plugins/tpm) (inner plugin management, see installation above)
