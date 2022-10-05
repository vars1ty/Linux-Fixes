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
Warning: Adding the `hash dbus-update...` line may prevent OBS Studio from working, ONLY use it if absolutely needed.

Usually only step 1 and 2 are needed, the rest is only in rare occasions.

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

## My OBS doesn't work on NVIDIA
Warning: I'd like to say that getting OBS working on NVIDIA without a headache is close to impossible, therefore I recommend you to use `wf-recorder` if you are just going to take small clips.

Second warning: This is focused on **Hyprland**, but it should apply to other wl-roots compositors as well.

First, thanks to PowerBall253 for the [initial guide](https://gist.github.com/PowerBall253/2dea6ddf6974ba4e5d26c3139ffb7580#unsupported-wl_shm-format-0x34324742-after-trying-to-share-the-screen-on-nvidia), follow it **before** this one, as mine focuses more on fixing issues.

1. Remove the gnome desktop portal, if installed. `sudo pacman -Rcsn xdg-desktop-portal-gnome`
2. If your Hyprland config contains lines like `dbus-update-activation-environment` and so on, comment them out.
3. If your config also contains lines where it starts `xdg-desktop-portal` services, comment them out.
4. Install the required packages: `pipewire wireplumber xdg-desktop-portal-wlr` + OBS Studio if not already installed
5. Run `systemctl --user enable pipewire.socket pipewire` and then make sure the services are all enabled: `systemctl --user status pipewire.socket pipewire`.
6. Install a patched NVIDIA package from the AUR, otherwise OBS will just sit there and not understand what NVENC is: `paru -S nvidia-utils-nvlax`
7. Restart your PC, not just your session.
8. Run Hyprland and open OBS Studio
9. If you followed the guide by PowerBall first and then this one, you should see `Screen Capture (PipeWire)` being available as a source.

![image](https://user-images.githubusercontent.com/54314240/191113950-646b6f78-05f9-4709-bcf7-d341329e32bb.png)

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

**For Flatpak applications to even run properly on Wayland, use Flatseal and remove the X11 Display Server permissions and only allow it to access Wayland.**
**Below is an example for Librewolf**

![image](https://user-images.githubusercontent.com/54314240/184984454-adffaf8a-e94e-4b67-bf18-6a1cd095e350.png)
## Screensharing on WebCord
No clue, for some "it just works" after configuring portals (as seen above with xdg-desktop-*), and for some it just will not work.

In general, screensharing and OBS is a hit or miss, since they both are moody as shit.
## Stremio has weird colors
In my case, I can't even run Stremio on Wayland without it suffering from issues. So instead, run it in XWayland for now.

For non-Flatpak users:
`export QT_QPA_PLATFORM=xcb && stremio` 

For Flatpak users:
`export QT_QPA_PLATFORM=xcb && com.stremio.Stremio`
## Neovide doesn't launch
This is due to some dep. issue IIRC, which hasn't yet been solved by Neovide.

For now, you have to run it in XWayland: `WINIT_UNIX_BACKEND=x11 neovide`
## PolKit is dead
For example, you may get this annoying error: `polkit-agent-helper-1: error response to PolicyKit daemon: GDBus.Error:org.freedesktop.PolicyKit1.Error.Failed: No session for cookie`

1. Install `polkit-gnome` (`sudo pacman -S polkit-gnome`)
2. Inside a startup script (or just run it in a terminal manually), run this: `exec /usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1 &`
3. Done

This seems to be a [bug](https://gitlab.freedesktop.org/polkit/polkit/-/issues/17) which still hasn't been fixed, as it only occurs for some commands.
## No default file manager
For example when you `Browse Folder` in Steam and it opens Terminal. Not limited to Wayland, but thought I may as well put it here.

1. Download a File Manager, for example `Nemo`
2. Assign the desired File Manager to be the default one using `xdg-mime default (name).desktop inode/directory`
3. Done
