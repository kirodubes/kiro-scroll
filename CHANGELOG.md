# Changelog

All notable changes to **kiro-scroll** are documented here.
Format: one dated entry per day (`YYYY.MM.DD`), newest first.

## 2026.09.05

### What Changed
- **Print now leaves a file behind.** The screenshot binds ran `grim … | wl-copy`, which put the
  image on the clipboard and nowhere else — no file, no notification, so the key looked dead next to
  chadwm's scrot bind that writes into `~/Pictures`. Both binds now call the shared
  `kiro-screenshot region` / `kiro-screenshot screen`, which saves a timestamped PNG in
  `~/Pictures/Screenshots`, still copies to the clipboard, and notifies with a thumbnail.

### Technical Details
- The helper is `/usr/bin/kiro-screenshot`, shipped by `kiro-wayland-dotfiles` — already a dependency
  of this package. The same clipboard-only line had been copy-pasted into twelve editions, so it now
  lives in exactly one place instead of being fixed twelve times.
- `scroll/config.d/60-keybindings`: `bindsym Print exec kiro-screenshot region`.
- **Build `kiro-wayland-dotfiles` first**: it provides the binary this edition's binds call.

### Files Modified
- `etc/skel/.config/scroll/config.d/60-keybindings`
- `etc/skel/.config/scroll/keybindings.txt`

## 2026.07.07

### Keyboard: US default + Alt+Shift layout toggle

**What Changed**
- Flipped the layout order `be,us` → **`us,be`**: US QWERTY is now the default at login, Belgian AZERTY the secondary layout.
- Added **`grp:alt_shift_toggle`** to the xkb options (now `grp:alt_shift_toggle,compose:caps`) so **Alt+Shift** switches `us`↔`be`. Before, the second layout was loaded but had no switch key — unreachable.

**Technical Details**
- `grp:alt_shift_toggle` matches the CachyOS Calamares reference (`keyboard/Config.cpp` defaults the group switcher to it when a second layout exists). `compose:caps` (Caps = Compose) unchanged. `xkb_layout` / `xkb_options` in `config.d/10-input`.

**Files Modified**
- `etc/skel/.config/scroll/config.d/10-input`

## 2026.07.05

### Fixed
- **Config-error red banner on first boot (`scrollnag`).** Three directives in `30-appearance` were
  taken from a newer `~/Public/scroll` `config.in` than the shipped `sway-scroll 1.12.15` accepts:
  `titlebar_border_radius 8` ("Invalid size specified") and `center_horizontal_if_fits` /
  `center_vertical_if_fits` ("Unknown/invalid command"). Removed all three (rounded corners already
  come from `default_decoration border_radius`; the center-if-fits defaults apply anyway). Verified
  on the riker test box: `scroll --validate` clean, live `scrollmsg reload` → `success`, banner gone.

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
