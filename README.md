# kiro-scroll

The **scroll desktop edition** of Kiro — the scrollable-tiling Wayland edition (sibling to
[kiro-sway](https://github.com/kirodubes/kiro-sway) and [kiro-niri](https://github.com/kirodubes/kiro-niri)).

## What it is

A configuration package: the source-of-truth config tree for Kiro's **scroll** edition.
[scroll](https://github.com/dawsers/scroll) is a fork of **sway** that replaces i3-style manual
tiling with a **scrolling / PaperWM-style** column layout (the same idea as niri, but built on the
sway codebase — so it keeps sway's config grammar and IPC). It ships the same **waybar + mako +
swaybg** shell as the rest of the Kiro Wayland line, and because scroll speaks sway's IPC (it sets
`SWAYSOCK`), waybar's native `sway/workspaces` module works unchanged — real workspace pills, no
workaround.

## What it ships

- `etc/skel/.config/scroll/config` + `config.d/*` — modular config: `10-input`, `20-output`,
  `30-appearance` (scroll's rounded corners + shadow + animations), `35-colors` (pywal-generated),
  `40-autostart`, `50-window-rules`, `60-keybindings`. Kiro SUPER grammar + CTRL+ALT +
  SUPER+F1..F12, plus scroll's scrolling verbs (`set_mode`, `cycle_size`, `align`, `fit_size`,
  overview, maximize).
- `etc/skel/.config/waybar/config-scroll.jsonc` — this edition's bar layout (native `sway/*`
  modules over `SWAYSOCK`).
- `etc/skel/.config/scroll/{keybindings.txt,bg/kiro.jpg,scripts/}` — cheat sheet, wallpaper, and
  the pywal `set-theme.sh` + `import-gsettings.sh`.

The shared shell files (`waybar/style.css` + `colors.css`, `mako/config`, `hypr/hyprlock.conf` +
`hypridle.conf`, and the dconf GTK defaults) come from **`kiro-wayland-dotfiles`**, a dependency —
so co-installing two Kiro Wayland editions never conflicts.

## Theming — pywal

One wallpaper drives every colour. At login `set-theme.sh` runs pywal on the wallpaper and fans the
palette out to waybar, mako, and the scroll window borders, then live-reloads each (`scrollmsg
reload`). Re-theme any time with `~/.config/scroll/scripts/set-theme.sh /path/to/wallpaper.jpg`.

## How to install

```sh
sudo pacman -S kiro-scroll
```

`kiro-scroll` depends on `sway-scroll` (the compositor, from the Kiro `nemesis_repo`),
`kiro-wayland-dotfiles`, the waybar stack, and `python-pywal`. On a fresh login scroll starts the
bar and wallpaper. Press **Super + Ctrl + S** for the searchable keybindings cheat sheet.

A pristine copy of the config is kept at `/usr/share/kiro/kiro-scroll/` so it can be restored.
