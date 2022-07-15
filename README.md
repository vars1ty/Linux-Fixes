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
2. Instead of launching Hyprland via the command `Hyprland`, add an alias like this:
`alias anticonsumer="bash -c '__GL_GSYNC_ALLOWED=1; __GL_VRR_ALLOWED=0; WLR_DRM_NO_ATOMIC=1; export QT_AUTO_SCREEN_SCALE_FACTOR=1; export QT_QPA_PLATFORM=wayland; export QT_WAYLAND_DISABLE_WINDOWDECORATION=1; export GDK_BACKEND=wayland,x11; export XDG_CURRENT_DESKTOP=Hyprland; export GBM_BACKEND=nvidia-drm; export __GLX_VENDOR_LIBRARY_NAME=nvidia; export MOZ_ENABLE_WAYLAND=1; export WLR_NO_HARDWARE_CURSORS=1; Hyprland'"`
3. Run the alias when you see your tty, in this example you'd run `anticonsumer` and then you should be in.
## Unity Hub crashes on NVIDIA GPUs
This is most lilely because of a `libva` error, to fix it on Arch, run these commands:

1. Install the unofficial NVIDIA VAAPI Driver from elFarto: `paru -S nvidia-vappi-driver-git`
2. Run Unity Hub using `LIBVA_DRIVER_NAME=nvidia unityhub`
3. Done
