<img src="assets/icon.svg" width="64" height="64" alt="">

# appImageInstaller

`appimg` installs AppImages so they show up in your application menu, with their own
icon, like any other app. It is a single bash script with no dependencies beyond
coreutils and awk, needs no root, and works on any distro.

## Install

```sh
./appimg setup
```

This copies `appimg` to `~/.local/bin`, then:

- adds **Install AppImage** to the right-click menu of every file manager it finds:
  Dolphin, Nemo and Thunar show it only on AppImages; GNOME Files and Caja show it
  under **Scripts** for all files, since they can't filter by type
- makes double-clicking an `.AppImage` install it

You get a desktop notification when an install finishes or fails. If you install a file
manager later, run `appimg setup` again.

## Usage

```sh
appimg ~/Downloads/Obsidian-1.6.7.AppImage   # install (or update)
appimg install --move Foo.AppImage           # move instead of copy
appimg list
appimg remove obsidian
appimg setup --remove                         # undo `appimg setup`
```

Installing an AppImage:

- copies it to `~/Applications/<name>.AppImage` and makes it executable
- adds its own launcher and icon to `~/.local/share/applications`
- links a command into `~/.local/bin` (e.g. `obsidian`)

Installing a newer version of the same app replaces the old one in place, so launchers
and pinned dock icons keep working. A running copy isn't disturbed.

Override locations with `APPIMG_DIR`, `APPIMG_BIN_DIR` and `XDG_DATA_HOME`.

## Troubleshooting

**`dlopen(): error loading libfuse.so.2`**: older AppImages need FUSE 2. `appimg`
warns about this at install time and prints the command for your distro:

| Distro | Command |
| --- | --- |
| Arch, Manjaro, EndeavourOS | `sudo pacman -S fuse2` |
| Ubuntu 24.04+, Debian 13+ | `sudo apt install libfuse2t64` |
| Ubuntu 22.04, Debian 12 | `sudo apt install libfuse2` |
| Fedora | `sudo dnf install fuse-libs` |
| openSUSE | `sudo zypper install libfuse2` |

**Electron app won't start on Ubuntu 24.04+** (`The SUID sandbox helper binary was
found, but is not configured correctly`): Ubuntu's AppArmor blocks the sandbox Electron
uses. Add `--no-sandbox` after the path on the `Exec=` line in
`~/.local/share/applications/<name>.desktop`. This turns off Chromium's sandbox for that
app.
