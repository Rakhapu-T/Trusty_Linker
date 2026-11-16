 Creating Desktop Icons on Linux (Ubuntu and Arch)

A reference sheet for making `.desktop` launchers: app-menu entries and desktop icons on Ubuntu and Arch Linux. The steps are the same on both unless a section says otherwise.

## 1. What a .desktop file is

A `.desktop` file is a small text file that tells the desktop environment how to launch a program: its name, icon, and the command to run. Ubuntu and Arch both follow the freedesktop.org desktop entry standard, so they read these files from the same folders to build the app menu, and copies of them on the Desktop show up as clickable icons.

## 2. Quick start template

```ini
[Desktop Entry]
Type=Application
Name=My App
Comment=Short description
Exec=/full/path/to/program %U
Icon=/full/path/to/icon.png
Terminal=false
Categories=Utility;
StartupNotify=true
```

Save it as `myapp.desktop`.

## 3. Field reference

| Field           | Meaning                                                                                   |
| --------------- | ----------------------------------------------------------------------------------------- |
| `Type`          | Always `Application` for a launcher                                                       |
| `Name`          | The label shown under the icon                                                            |
| `Comment`       | Tooltip text                                                                              |
| `Exec`          | The command to run. Use the **full absolute path**. `~` and `$HOME` do not work here      |
| `Icon`          | Absolute path to a PNG or SVG, or an icon theme name                                      |
| `Terminal`      | `true` opens a terminal window for the program, `false` runs it without one               |
| `Categories`    | Which app menu section it appears in (for example `Utility;`, `Development;`, `Network;`) |
| `Path`          | Optional. The working directory the program starts in                                     |
| `StartupNotify` | Optional. Shows the busy cursor while the app starts                                      |

### Exec field codes

These are placeholders the desktop fills in. Remove them if you don't need them.

| Code | Meaning |
| --- | --- |
| `%U` | One or more URLs |
| `%F` | One or more file paths |
| `%u` / `%f` | A single URL / file path |

## 4. Installing it

1. Save the file to your personal launcher folder: `~/.local/share/applications/myapp.desktop`
2. Make it executable:

   ```bash
   chmod +x ~/.local/share/applications/myapp.desktop
   ```
3. For a desktop icon, copy it to the desktop:

   ```bash
   cp ~/.local/share/applications/myapp.desktop ~/Desktop/
   ```
4. On GNOME, mark it as trusted (or right-click it and choose **Allow Launching**):

   ```bash
   gio set ~/Desktop/myapp.desktop metadata::trusted true
   ```
5. Refresh the menu if the entry doesn't appear:

   ```bash
   update-desktop-database ~/.local/share/applications
   ```

Installing system-wide instead uses `/usr/share/applications/`, which needs `sudo`.

## 4b. Ubuntu vs Arch differences

The launcher file and its folders are identical on both distros. Only package names and desktop environment defaults differ.

| Task | Ubuntu | Arch |
| --- | --- | --- |
| Install `desktop-file-validate` and `update-desktop-database` | `sudo apt install desktop-file-utils` | `sudo pacman -S desktop-file-utils` |
| Install `gtk-launch` | `sudo apt install libgtk-3-bin` | `sudo pacman -S gtk3` |
| Install `gio` | `sudo apt install libglib2.0-bin` | `sudo pacman -S glib2` |
| Run AppImages (needs FUSE 2) | `sudo apt install libfuse2t64` | `sudo pacman -S fuse2` |
| Find the Desktop folder | `xdg-user-dir DESKTOP` | `sudo pacman -S xdg-user-dirs`, then `xdg-user-dir DESKTOP` |
| Default desktop | GNOME | None. It depends on what you installed (KDE Plasma, GNOME, Xfce and others) |

On Ubuntu, most of these are already installed with the default desktop. Install only what is missing.

Use `xdg-user-dir` instead of typing `~/Desktop`, since the folder name can differ by language and setup:

```bash
cp ~/.local/share/applications/myapp.desktop "$(xdg-user-dir DESKTOP)/"
```

### Trusting the desktop icon, by desktop environment

| Desktop | What to do |
| --- | --- |
| GNOME | `gio set "$(xdg-user-dir DESKTOP)/myapp.desktop" metadata::trusted true`, or right-click and choose **Allow Launching** |
| KDE Plasma | Run `chmod +x` on the file. If Plasma asks whether to trust it on first launch, accept |
| Xfce | Run `chmod +x` on the file. If it asks, right-click and choose **Allow Launching** |

Menu wording varies between versions, so treat these as a guide and check your own desktop if a step doesn't match.

Before writing a launcher for something you installed through a package manager (`apt`, `pacman` or the AUR), check whether it already has one:

```bash
ls /usr/share/applications | grep -i myapp
```

## 5. Setting environment variables

Launchers started from the app menu or desktop do **not** read `~/.bashrc`, so variables you export there won't reach the program. Two ways to set them.

**Option A: use `env` in the Exec line**

```ini
Exec=env MY_VAR="value" /full/path/to/program
```

**Option B: use a wrapper script** (better for long values or several variables, since quoting inside `Exec` can be fussy)

```bash
#!/bin/bash
export MY_VAR="value"
exec /full/path/to/program "$@"
```

Save it (for example `~/bin/myapp-launch.sh`), run `chmod +x` on it, then point `Exec=` at the script.

## 6. Example: QGroundControl with a joystick mapping fix

This is the case that needs an environment variable, because the SDL joystick mapping has to exist before QGC starts.

```ini
[Desktop Entry]
Type=Application
Name=QGroundControl
Exec=env SDL_GAMECONTROLLERCONFIG="03001f4009120000544f000011010000,OpenTX Radiomaster Zorro Joystick,a:b0,b:b1,x:b3,y:b4,back:b10,guide:b12,start:b11,leftstick:b13,rightstick:b14,leftshoulder:b6,rightshoulder:b7,leftx:a0,lefty:a1,rightx:a2,righty:a3,lefttrigger:a4,righttrigger:a5,crc:401f,platform:Linux" /home/USERNAME/path/to/QGroundControl.AppImage
Icon=/home/USERNAME/path/to/qgc-icon.png
Terminal=false
Categories=Utility;
```

Replace `USERNAME` and the paths with your real ones. The AppImage itself must be executable (`chmod +x QGroundControl.AppImage`).

## 7. AppImages and other portable programs

- Make the file executable with `chmod +x`.
- Point `Exec=` at the AppImage's absolute path.
- AppImages don't install an icon for you. Download or extract a PNG and point `Icon=` at it.
- If you move the file, update `Exec=` and `Icon=`, since the launcher won't follow it.

AppImages need FUSE 2 on both distros. See the package names in section 4b.

## 8. Apps inside a Distrobox container

A `.desktop` file on the host that runs a program inside the container needs to enter the container first. The usual shortcut is to let Distrobox generate the launcher from inside the container:

```bash
distrobox-export --app myapp
```

Check `distrobox-export --help` for the exact options on your version.

## 9. Troubleshooting

| Symptom | Likely cause and fix |
| --- | --- |
| Icon shows a gear or blank | `Icon=` path is wrong. Use an absolute path to a PNG or SVG |
| Double-click does nothing | File isn't executable or isn't trusted. Run `chmod +x` and `gio set ... metadata::trusted true` |
| It says "untrusted application launcher" | Right-click and choose **Allow Launching** |
| Doesn't appear in the app menu | Wrong folder, or run `update-desktop-database ~/.local/share/applications` |
| Works in a terminal but not from the icon | Missing environment variable or a relative path. Use full paths and see section 5 |
| `~` or `$HOME` in `Exec` doesn't work | `Exec` doesn't expand them. Write the full path |

### Useful debug commands

```bash
# Launch it the way the desktop does and show errors
gtk-launch myapp

# Check the file for mistakes
desktop-file-validate ~/.local/share/applications/myapp.desktop

# Install the validator if missing
sudo apt install desktop-file-utils      # Ubuntu
sudo pacman -S desktop-file-utils        # Arch
```

`gtk-launch` takes the file name without the `.desktop` extension.

## 10. Checklist for a new launcher

- [ ] Program path is absolute and the program is executable
- [ ] Icon path is absolute and the image exists
- [ ] Environment variables set through `env` or a wrapper script
- [ ] Saved in `~/.local/share/applications/` with a `.desktop` extension
- [ ] `chmod +x` run on the file
- [ ] Copied to `~/Desktop/` and marked trusted if you want a desktop icon
- [ ] Tested with `gtk-launch`
