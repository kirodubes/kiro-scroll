# Changelog

All notable changes to **kiro-scroll** are documented here.
Format: one dated entry per day (`YYYY.MM.DD`), newest first.

## 2026.07.05

### What Changed
- **Initial config package for the Kiro scroll edition.** Stands up the scrollable-tiling Wayland
  edition built on [scroll](https://github.com/dawsers/scroll) (a fork of sway with a PaperWM-style
  scrolling column layout). Modelled on `kiro-sway` — scroll shares sway's config grammar and IPC —
  with the scrolling-layout differences authored from scroll's own vocabulary.

### Technical Details
- **Config tree** `etc/skel/.config/scroll/config` + `config.d/{10-input,20-output,30-appearance,
  35-colors,40-autostart,50-window-rules,60-keybindings}`, loaded via a sorted glob.
- **Keybindings rebuilt, not ported.** Kiro's SUPER launcher grammar (CTRL+ALT + SUPER+F1..F12 +
  workspaces + system) is identical to kiro-sway for cross-edition muscle memory; the
  window-management section is authored from scroll's verbs — `set_mode h/v` (scroll direction),
  `cycle_size` (column/row sizing), `align`/`fit_size`/`jump` modes, `scale_workspace overview`,
  `toggle_size` (maximize) — on chords free in the Kiro grammar. Kiro launcher chords win; scroll
  verbs take free chords, so the duplicate-chord scan passes by design.
- **waybar** ships as `config-scroll.jsonc` using the native `sway/workspaces`/`sway/mode`/
  `sway/window` modules — scroll's `ipc-server.c` sets `SWAYSOCK` as well as `SCROLLSOCK`, so they
  work unchanged.
- **Appearance** uses scroll's own `default_decoration` (rounded corners + shadow) and `animations
  {}` block — scroll is not swayfx, so no `blur`/`corner_radius`/`layer_effects`.
- **pywal theming** via `scripts/set-theme.sh` (waybar SIGUSR2 + `makoctl reload` + `scrollmsg
  reload`); GTK mirror via `scripts/import-gsettings.sh`.
- **`autotiling` dropped** — it picks i3 split direction, meaningless in scroll's scrolling layout.
- **Shared base** via `depends=('kiro-wayland-dotfiles')` (mako, hyprlock/hypridle, waybar
  style/colors, dconf) — no shared files shipped here, so co-installing editions never conflicts.

### Files Modified
- `etc/skel/.config/scroll/` (new), `etc/skel/.config/waybar/config-scroll.jsonc` (new)
- `README.md`, `CLAUDE.md`, `CHANGELOG.md`, `up.sh`, `setup.sh`, `.gitignore`, `kiro.jpg` (new)
