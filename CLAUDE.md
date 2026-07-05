# kiro-scroll — Claude project instructions

## Overview
Config package for the **Kiro scroll edition** — the scrollable-tiling Wayland edition of the
KIROTUX line (sibling to [kiro-sway](../kiro-sway/CLAUDE.md) and the niri editions). Public,
open-core, shipped via `nemesis_repo`. Full research + decisions live in the internal
`Kiro-HQ/Kirotux/study-of-scroll.md`.

## Edition spec (the WM-variable matrix)
- **Compositor:** **scroll** ([dawsers/scroll](https://github.com/dawsers/scroll)) — a fork of sway
  with a PaperWM-style scrolling column layout. Packaged in the AUR as **`sway-scroll`**; Erik
  builds it into `nemesis_repo` (not in extra/chaotic-aur/cachyos), so this package just `depends`
  on `sway-scroll`. The `scroll` binary hardcodes the `~/.config/scroll/` config dir regardless of
  package name.
- **Config language:** i3/sway-style (`ft=swayconfig`), **modular** `~/.config/scroll/config`
  (entrypoint: variables + `include config.d/*`) → `config.d/NN-*` snippets (sorted-glob load
  order). Edit the snippet, not the entrypoint.
- **Desktop shell:** **waybar + mako + swaybg** — same stack as kiro-sway. scroll's `ipc-server.c`
  sets **both `SWAYSOCK` and `SCROLLSOCK`** to the same socket, so waybar's native `sway/workspaces`
  module works unchanged (no wlr/taskbar workaround). Ships only `waybar/config-scroll.jsonc`; the
  shared css/style comes from `kiro-wayland-dotfiles`.
- **Autostart:** scroll's `exec`/`exec_always` in `config.d/40-autostart` (run via `/bin/sh -c`).
  **No `autotiling`** — it picks i3 split direction, meaningless in scroll's scrolling layout.
  XDG-autostart-only items (`xdg-user-dirs-update`) added by hand.
- **Theming:** **pywal**. `set-theme.sh` runs pywal at login → regenerates `waybar/colors.css`,
  rewrites mako + the scroll `config.d/35-colors` block, then reloads (waybar SIGUSR2, `makoctl
  reload`, `scrollmsg reload`). Same pipeline shape as kiro-sway; only the IPC reload command
  changed (`swaymsg` → `scrollmsg`).
- **Lock/idle:** hyprlock + hypridle (wlroots-compatible), from `kiro-wayland-dotfiles` — matches
  kiro-sway/kiro-wayfire for cross-edition lock consistency.
- **Dependencies:** `sway-scroll` from `nemesis_repo`; `kiro-wayland-dotfiles`; everything else from
  `extra` / the Kiro repos. No 3PARTY builds beyond `sway-scroll` (which Erik owns).

## Keybindings
- SUPER launcher grammar identical to kiro-sway (`bindsym`): CTRL+ALT launchers + SUPER+F1..F12 +
  `kiro-keybindings` on SUPER+CTRL+S. Workspaces/system/multimedia/wallpaper sections unchanged.
- **Window management rebuilt in scroll's vocabulary** (scroll has no split/tabbed/stacking):
  arrows = focus (scroll columns), SUPER+Shift+arrows = move, SUPER+Home/End = focus row start/end,
  SUPER+`[`/`]` = `set_mode h/v` (scroll direction), SUPER+`-`/`=` = `cycle_size` (column/row size),
  SUPER+M = maximize (`toggle_size`), SUPER+grave = `scale_workspace overview`, SUPER+C = `align`
  mode, SUPER+W = `fit_size` mode, SUPER+/ = `jump` mode.
- **Collision rule:** Kiro launcher chords win; scroll verbs take chords free in the Kiro grammar,
  so the duplicate-chord scan passes by design. Mode-internal `bindsym`s (inside `mode "align"` etc.)
  are scoped and do not collide with global binds — the scan checks global binds only.
- `etc/skel/.config/scroll/keybindings.txt` mirrors `config.d/60-keybindings` — keep in lockstep.
  (`kiro-keybindings` / `/kiro-create-keybindings` still need **scroll** in their WM-detection
  table — known gap, same one hyprland/niri/sway/wayfire hit; `keybindings.txt` was authored by
  hand here.)

## Patterns / gotchas
- **scroll is NOT swayfx.** No `blur`/`corner_radius`/`layer_effects`. `30-appearance` uses scroll's
  own `default_decoration border_radius … shadow …` + an `animations {}` block. Don't paste swayfx
  effect lines — they error on scroll.
- **`scrollmsg`, not `swaymsg`** for IPC (reload etc.). `scrollnag` is the nag tool (unused here —
  Kiro uses `archlinux-logout`).
- **`app_id` not `class`** in `for_window` rules — `class` only matches XWayland clients.
- **Wallpaper is set once** by swaybg in `40-autostart`; the native `output * bg` line is left out
  to avoid a double-set race.
- **Scrolling-layout column sizes** come from `layout_widths`/`layout_heights` in `30-appearance`;
  `cycle_size` steps through them.
- Config **not yet validated on a real scroll boot** — run `scroll --validate` (or `scroll -C`) on a
  real session before the first ISO test (same caveat niri/sway/wayfire carried). scroll wasn't
  installed on the build box; a structural brace/chord check passed but won't catch a mistyped
  scroll verb.

## Build / delivery
- Source-of-truth for the config; delivered as the `kiro-scroll` package via
  `../KIROTUX-PKG-BUILD/kiro-scroll/build.sh` (public recipe → `~/EDU/nemesis_repo/`). After editing
  here: rebuild the package (recipe `build.sh`), then the ISO to test a fresh install.
- See [../CLAUDE.md](../CLAUDE.md) for the full KIROTUX delivery architecture.
