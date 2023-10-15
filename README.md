# Linux Fixes
## Jetbrains Products
Because Jetbrains chose to use *Java* (yeah I know, cringe language) as the language behind their IDEs, it doesn't render properly (or at all) on Wayland.

To overcome this, run this in Terminal: `export _JAVA_AWT_WM_NONREPARENTING=1 && rider` - You may also pass the `export` into the desktop entry so that you can skip opening Terminal.

And before you say, "Java is a good language! BOO BOO SOB SOB" - No, it isn't.
## Maim doesn't work
Maim is a mess anyways, freezing and dying left and right on X11. Use Grim instead if you are on Wayland.

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
> **Warning**: Adding the `hash dbus-update...` line may prevent OBS Studio from working, ONLY use it if absolutely needed.

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

## My OBS doesn't work on Wayland, NVIDIA
Warning: Setting up OBS on Wayland in general is a hit or miss.

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

**ALTERNATIVE**: Install `wlrobs-hf` from the AUR, add the `Wayland output(dmabuf)` source and done.

## Screenshare doesn't work on Firefox
If you set up your portals properly like above and it still doesn't work, launch this inside a terminal and try screenshare: `/usr/lib/xdg-desktop-portal -r`

## GTK Themes not applying everywhere
> **Warning**: Incomplete section, use at your own risk!

Let's say you are using the [Orchis](https://github.com/vinceliuice/Orchis-theme) theme and want it to apply to applications such as Thunar. Well, does this look correct?

![image](https://user-images.githubusercontent.com/54314240/183556600-779f7abd-42d5-4d99-aee2-321babea71b2.png)

No? It hurts your eyes right? Yeah thought so.
1. Install the theme of your liking, using Orchis in this example with Thunar as the file manager
2. Open up a terminal and write `export GTK_THEME=Orchis:dark` -- ThemeName:Variant
3. Write `thunar` to open Thunar and congrats, your theme is there and you saved your eyes!

![image](https://user-images.githubusercontent.com/54314240/183556907-14494abe-c601-4273-8966-91951be86ec3.png)


You may wanna pass this to `/etc/environment` though so you don't have to export the variable yourself constantly.
## Cursor Themes not applying in Flatpak applications
1. Open up Flatseal
2. Press `All Applications`
3. In `Other files` add in `/home/YOUR_USERNAME/.icons`
4. Done, now you should see the cursor in Flatpak applications.
## Help help, Electron applications aren't running! Passthrough errors etc
No idea if this is tied to NVIDIA GPUs, but seems like it is. Anyways, launch your RAM-Hog application with these flags:

`application-name --enable-features=UseOzonePlatform --ozone-platform=wayland --use-gl=egl`

I have no idea why this happens for some, while for others it just works. But it's whatever I guess.
## NVIDIA Overclocking does not work on Wayland
Blame NVIDIA, there are ways around it, like starting an X11 session, applying OC and then going back to your Wayland session, but it's all a huge mess.

Go AMD instead.
## Discord is slow and laggy
Don't use the official Discord client, nor the AUR hacky electron-upgraded ones.

Instead, use [ArmCord](https://github.com/ArmCord/ArmCord) which wraps around the web version of Discord, while also respecting your privacy, blocks certain trackers and has plugin-support.

> **Note**: There's also WebCord, but it tends to break after updates with weird errors, and it also doesn't have straight-forward support for plugins.
## Do I have to pass flags to every Electron application for it to run in Wayland?
Yes and no. 

No if you make a file at `~/.config/electron-flags.conf` with the following contents:
```
--enable-features=UseOzonePlatform
--ozone-platform=wayland
```

*Although it's worth noting that a lot of applications tend to not give a shit about that file.*

Yes if the application is either from Flatpak and doesn't have read permissions for the file, **or** if the application simply just does not want to run on Wayland at all.

**For Electron Flatpak applications to even run properly on Wayland, use Flatseal and remove the X11 Display Server permissions and only allow it to access Wayland.**
**Below is an example for Librewolf**

![image](https://user-images.githubusercontent.com/54314240/184984454-adffaf8a-e94e-4b67-bf18-6a1cd095e350.png)
## Stremio has weird colors on Wayland
In my case, I can't even run Stremio on Wayland without it suffering from issues. So instead, run it in XWayland for now.

For non-Flatpak users:
`export QT_QPA_PLATFORM=xcb && stremio` 

For Flatpak users:
`export QT_QPA_PLATFORM=xcb && com.stremio.Stremio`

Ensure you have `qt5-wayland` and `qt5ct` installed BEFORE running any of the steps above.
## Neovide doesn't launch on Wayland
Neovide doesn't have proper Wayland support due to some outdated (IIRC) dependency issue.

There are, however, [forks](https://github.com/williamspatrick/neovide) of it that run okay-ish, flaws will be listed below:
1. When dragging with your cursor, the position is offset. This doesn't affect just regular single-clicks though
2. Can crash in some rare occurrences
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
## Steam crashes with `cannot create directory /home/username/.steam : Read-only filesystem`
This took me **days** to even figure out, don't ask why and how I even got to this point because I don't know either.

1. Install Steam through Flatpak
2. Make a new directory at your home called `.steam`, or copy-paste this: `mkdir ~/.steam`
3. Take ownership of it using `chown $(whoami):$(whoami) ~/.steam`
4. Ensure Flatpak has read-write permissions of that directory (`rw`) by executing `sudo flatpak override com.valvesoftware.Steam --filesystem=/home/$(whoami)/.steam:rw`
5. Launch Steam

If all went accordingly, Steam should launch and work. If not, then I have no idea what your Steam is on.
## Proton doesn't work with EAC
If you directly run a game through Bottles that isn't from Flatpak, then you might have noticed that you can't get into a game (or you get kicked after 30 seconds).

This is because Proton doesn't have the Easy AntiCheat runtime set by default, you'll have to do that yourself.

1. Install Steam
2. Install `Proton EasyAntiCheat Runtime`
3. Right-Click EasyAntiCheat Runtime -> Manage -> Browse local files
4. Copy the absolute path to the folder, then set a new environment variable inside of Bottles named `PROTON_EAC_RUNTIME`
5. Paste in the EasyAntiCheat Runtime path into the newly-created environment variable, and save
6. Run your game and gg's, it should work.

This should also work for BattlEye, using the `PROTON_BATTLEYE_RUNTIME` environment variable.
## Cursor Themes not working
If you set your cursor theme through LXAppearance and can't see it across all of your applications, then it didn't get set properly.

1. Locate the **exact** name of the cursor theme, it's listed in LXAppearance -> Mouse Cursor
2. Open Terminal and write in `gsettings set org.gnome.desktop.interface cursor-theme (name-of-theme)`
3. Open `~/.config/gtk-3.0/settings.ini` in your favorite text editor
4. Set the value of `gtk-cursor-theme-name` to the cursor themes name
5. Restart and done!
## I can't control my PWM Fans
Easy fix for most PCs:

1. Edit your kernel parameters
2. Add `acpi_enforce_resources=lax`
3. Regenerate your GRUB config
4. Restart and done, they should work after you have detected them using `sensors-detect`
## Firefox CSS Blur doesn't work on Hyprland
> **Note** This isn't a complete "fix", but more a workaround.
>
> It's also expected that you have already enabled Web Render, etc.

1. Make sure your theme is actually making the main window transparent, which is what Hyprland will be blurring
2. Open your Hyprland config
3. Add this line: `windowrule=opacity 0.999,firefox` - `firefox` being the class name of the browser.
4. Restart your browser
5. Done

It's hacky though, as the blur will go away when you fullscreen the browser, or focus a different window.
## Splitgate crashes on AMD GPUs
1. Open `~/.config/Epic/PortalWars/Saved/Config/LinuxNoEditor/GameUserSettings.ini`
2. Set `bHasRunAutoCalibration` to `True`
3. Save and Splitgate should now work
## Crossout crashes
> **Warning**: Incomplete section, use at your own risk!

1. Install Crossout via Bottles
2. Set the runner to the latest Proton-GE version
3. Set the DXVK Version to 1.10.3
4. Enable Virtual Desktop (or Gamescope)
5. Launch
## AMD Vulkan initialization errors
Remove the `amdvlk` package and it should work
## Missing sudo / broken doas config
- Option 1: Use an USB and chroot into your machine.
- Option 2: Run `pkexec`, enter your password and now you *should* be running commands as root, so fix what you broke and then `exit`.
## Failed to initialize Wayland platform
Electrooooon!

1. Run `XDG_RUNTIME_DIR=/run/user/1000 (application)`
2. If it works, proceed. If not, I have no idea what your issue is related to if you setup the flags correctly (aka UseOzonePlatform etc)
3. Add `XDG_RUNTIME_DIR=/run/user/1000` to `/etc/environment` and reboot

Done!
## I can't enter my keyboards bluetooth passkey
1. Make sure you have followed the Arch Wiki regarding bluetooth, and what packages to install.
2. `sudo modprobe btusb`

Bluetoothctl commands:
1. `power on`
2. `pairable on`
3. `scan on`
4. `agent off`
5. `agent NoInputNoOutput`
6. `trust (keyboard-mac)`
7. `pair (keyboard-mac)`
8. Done, it should now be connected
## My Firefox CSS theme has no blur?
1. Add these lines into your theme:
```css
#TabsToolbar, #titlebar, #main-window, #navigator-toolbox {
	-moz-appearance: none !important;
	background-color: rgba(255, 255, 255, 0) !important;
	background-image: none !important;
}
```
2. If you're using Hyprland, add this window rule to make the blur not become solid: `windowrule=opacity 0.999,NAME`, for example on Mullvad Browser, it's: `windowrule=opacity 0.999,Mullvad Browser`.
