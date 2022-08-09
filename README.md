# Wayland Fixes
Fixes for various Wayland-related problems, because icbf searching for them again.
## Jetbrains Products
Because Jetbrains chose to use *Java* (yeah I know, cringe language) as the language behind their IDEs, it doesn't render properly (or at all) on Wayland.

To overcome this, run this in Terminal: `export _JAVA_AWT_WM_NONREPARENTING=1 && rider` - You may also pass the `export` into the desktop entry so that you can skip opening Terminal.
## Maim doesn't work
Maim is a mess anyways, freezing and dying left and right on X11. Use Grim instead.

Packages: `grim`, `grimshot`

To take a screenshot, run this command (bind it to a key): `grimshot copy area`
## NVIDIA doesn't launch on Hyprland
What a surprise.

1. Add the nvidia modeset option to your kernel parameters and load the modules early, [link](https://wiki.archlinux.org/title/NVIDIA#DRM_kernel_mode_setting)
2. Add these lines to `/etc/environment`:
```
export LIBVA_DRIVER_NAME=nvidia
export CLUTTER_BACKEND=wayland
export XDG_SESSION_TYPE=wayland
export QT_WAYLAND_DISABLE_WINDOWDECORATION=1
export MOZ_ENABLE_WAYLAND=1
export GBM_BACKEND=nvidia-drm
export __GLX_VENDOR_LIBRARY_NAME=nvidia
export WLR_NO_HARDWARE_CURSORS=1
export WLR_BACKEND=vulkan
export QT_QPA_PLATFORM=wayland
export GDK_BACKEND=wayland
```
3. Restart and run Hyprland
## Unity Hub crashes on NVIDIA GPUs
This is most lilely because of a `libva` error, to fix it on Arch, run these commands:

1. Install the unofficial NVIDIA VAAPI Driver from elFarto: `paru -S nvidia-vappi-driver-git`
2. Run Unity Hub using `LIBVA_DRIVER_NAME=nvidia unityhub`
3. Done
## Flatpak Apps can't open FileChooser, etc
Note: This is only for Hyprland, it may or may not work for other Wayland compositors.
***

1. Install `xdg-desktop-portal`, `xdg-desktop-potal-gtk` and `xdg-desktop-portal-wlr`
2. Add an alias to your `.bashrc` or `.zshrc` named `hyprland`, here's the full line:
   * `alias hyprland="dbus-run-session Hyprland"`
3. Add this to your config:
```
exec-once=systemctl --user enable xdg-desktop-portal
exec-once=systemctl --user enable xdg-desktop-portal-wlr
exec-once=systemctl --user enable xdg-desktop-portal-gtk
exec-once=hash dbus-update-activation-environment 2>/dev/null && dbus-update-activation-environment --systemd DISPLAY WAYLAND_DISPLAY XDG_CURRENT_DESKTOP
```
4. Done, restart your PC and it should be working again. Make sure you launch Hyprland using `hyprland`, or you can just use  a `.desktop` file with `dbus-run-session` inside the `exec` part.

## My OBS doesn't work :'(
I never got OBS to work properly under Wayland (well dmabuf or w/e works until you switch workspaces), so I just started using `wf-recorder` instead.

Package: `wf-recorder`

Command to record with audio: `wf-recorder -a`, and to record without audio: `wf-recorder`
## GTK Themes not applying everywhere
I don't know if this is a Wayland-specific issue, but I have only encountered it on Wayland, hence why it's here.
***
Let's say you are using the [Orchis](https://github.com/vinceliuice/Orchis-theme) theme and want it to apply to applications such as Thunar. Well, does this look correct?

![image](https://user-images.githubusercontent.com/54314240/183556600-779f7abd-42d5-4d99-aee2-321babea71b2.png)

No? It hurts your eyes right? Yeah thought so.
1. Install the theme of your liking, using Orchis in this example with Thunar as the file manager
2. Open up a terminal and write `export GTK_THEME=Orchis:dark` -- ThemeName:Variant
3. Write `thunar` to open Thunar and congrats, your theme is there and you saved your eyes!

![image](https://user-images.githubusercontent.com/54314240/183556907-14494abe-c601-4273-8966-91951be86ec3.png)


You may wanna pass this to `/etc/environment` though so you don't have to export the variable yourself constantly.
