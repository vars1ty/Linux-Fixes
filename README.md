# Wayland Fixes
Fixes for various Wayland-related problems, because icbf searching for them again.
## Jetbrains Products
Because Jetbrains chose to use *Java* (yeah I know, cringe language) as the language behind their IDEs, it doesn't render properly (or at all) on Wayland.

To overcome this, run this in Terminal: `export _JAVA_AWT_WM_NONREPARENTING=1 && rider` - You may also pass the `export` into the desktop entry so that you can skip opening Terminal.

And before you say, "Java is a good language! BOO BOO SOB SOB" - No, it isn't.
## Maim doesn't work
Maim is a mess anyways, freezing and dying left and right on X11. Use Grim instead.

Packages: `grim`, `grimshot`

To take a screenshot, run this command (bind it to a key): `grimshot copy area`
## NVIDIA doesn't launch on Hyprland
What a surprise.

**Update**: Moved instructions over to Hyprland's [wiki](https://wiki.hyprland.org/Nvidia/).
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
## Cursor Themes not applying in Flatpak applications
I don't know if this is a Wayland-specific issue, but I have only encountered it on Wayland, hence why it's here.
***
1. Open up Flatseal
2. Press `All Applications`
3. In `Other files` add in `/home/YOUR_USERNAME/.icons`
4. Done, now you should see the cursor in Flatpak applications.
## Help help, Electron applications aren't running! Passthrough errors etc
No idea if this is tied to NVIDIA GPUs, but seems like it is. Anyways, launch your RAM-Hog application with these flags:

`application-name --enable-features=UseOzonePlatform --ozone-platform=wayland --use-gl=egl`

I have no idea why this happens for some, while for others it just works. But it's whatever I guess.
## NVIDIA Overclocking does not work
Blame NVIDIA, there's (afaik) no workaround for it atm.
## Discord is slow and laggy
Don't use the official Discord client, nor the AUR hacky electron-upgraded ones.

Instead, use [WebCord](https://github.com/SpacingBat3/WebCord) which wraps around the web version of Discord, while also respecting your privacy and blocking certain tracking.
## Do I have to pass flags to every Electron application for it to run in Wayland?
Yes and no. 

No if you make a file at `~/.config/electron-flags.conf` with the following contents:
```
--enable-features=UseOzonePlatform
--ozone-platform=wayland
```

Yes if the application is either from Flatpak and doesn't have read permissions for the file, **or** if the application simply just does not want to run on Wayland at all.

**For Flatpak applications to even run properly on Wayland, use Flatseal and remove the X11 Display Server settings and only allow it to access Wayland.**
## Screensharing on WebCord
No clue, for some "it just works" after configuring portals (as seen above with xdg-desktop-*), and for some it just will not work.

In general, screensharing and OBS is a hit or miss, since they both are moody as shit.
