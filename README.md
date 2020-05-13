# tse-linux-workshop

This guide will walk you through how to take a barebones Arch Linux installation
and customize it to your liking. It'll also go over a bit of kernel compilation.

## VirtualBox Setup

Before we begin, you must have **at least 6-7 GB of free disk space** available.

The first step is to download the OVA file for the VirtualBox. This is basically
just a compressed version of the virtual hard drive along with some metadata. 
It's a 2GB file, so the download might take some time.

https://drive.google.com/open?id=1CR3ot6dPG2KJt3ZMXwKF24Q2Syy4mi-S

After you've acquired the OVA file, you can open VirtualBox. Go to the top left
of the corner and click on "Import Applicance". It should prompt you to look
for the OVA file you just downloaded. Just follow the steps as it walks you
through the import process.

Once you've imported and started the virtual machine successfully, you can
go ahead and delete the OVA file because it won't be needed anymore.

## Logging In

The login credentials are listed below:

* Username: tse
* Password: .

Note that the password is literally just a period. This should
make it easy to switch to superuser access.

After you login, you should be greeted with a bash shell. Type in

```
echo $SHELL
```

It should print "/bin/bash". Try `$XDG_SESSION_TYPE` too; it should be "tty".

## Required Packages

The following packages should already be present on your system. Most of this guide
will focus on how to set them up.
* xorg
* i3
* rxvt-unicode
* zsh
* polybar
* asp

If, for some reason, you find that one of these packages isn't installed, you can do so
with:
```
yay -S <PACKAGE NAME>
```

For reference, you can update the entire system using:
```
yay -Syu
```

However, I wouldn't recommend doing this, because it could take a long time, depending on
how many packages have been updated since I last ran the command.

## Swapping Shells

First, let's stop using the bash shell. Instead, let's try switching to the fish shell.
fish should already be installed on your machine, so try running it with:

```
fish
```

You should notice some immediate improvements. There is now color highlighting, and
there is also tab-based autocompletion for both commands and directories. Try typing
in "bas" and pressing TAB. It should allow you to cycle through the various commands
prefixed with "bas" including "bash". If you press ENTER

Now, let's replace bash with fish for our default shell. Type in:

```
chsh
```

When it prompts for the location of your new shell, use `/bin/fish`.

Now, log out by pressing CTRL-D twice. The first CTRL-D will log you out of fish
and back into your original bash shell. The second CTRL-D will log you out of
your bash shell back into the login screen. When you relog, you should spawn
a fish shell instead of a bash shell.

## Enabling i3

In order for any window manager to work, you first need to do two things:
1. You need to tell the X server what window manager to run.
2. You need to start the X server.

Let's address the first point. To do that, we need to create something 
called an `xinitrc` file. Type in the following command to get a default 
xinitrc:

```
cp /etc/X11/xinit/xinitrc ~/.xinitrc
```

Let's modify the xinitrc file to tell it to open i3. Open the file in vim via 
`vim ~/.xinitrc`. Go to the bottom of the xinitrc file and you should see
the following text:

```
twm &
xclock ...
xterm ...
xterm ...
xterm ...
exec xterm ...
```

Delete all of those lines and replace it with this:

```
exec i3
```

xinitrc is a shell script that the X server will run on start. The default script tells 
the X server to first load some configuration files (your .Xresources file notably,
more on this later), and then to start up a window managed called **twm** and then
open up some **xterm** terminals. We won't be using either twm or xterm, so on our machine
if you ran the default commands, the X server would just crash because those aren't valid.
Instead, we want to run the i3 command, which starts the i3 window manager and connects it
to the X server.

Exit out of vim. Now, we need to start the X server (and the i3 window manager). Type in the
following command:

```
startx
```

You'll notice some immediate changes.

## Learning i3 Key Bindings

Since it's your first time starting i3wm, you'll get a prompt to generate a configuration file.
Press 'yes' to this question.

Then, you're going to want to select a modifier key. A good choice for this is a Windows key. If
this isn't suitable for you, then press the down arrow to select the ALT key as your modifier.
Your modifier key is very important, so choose this wisely (but it is configurable).

After that, you should see just a black screen with a status bar at the bottom. From left to
right, this displays your workspaces, Internet connection, memory, disk usage, and current time.

Now, let's open a terminal or two! Press the following key combination:

```
$MOD + $ENTER
```

where $MOD is the modifier key you selected earlier. Also, don't type the '+', just press ENTER
with your MOD key.

You should see a white terminal open up. Press $MOD + $ENTER a few more times and a few more
terminals should pop up. They're all going to fold out horizontally.

Let's try opening a terminal vertically. Press:

```
$MOD + $ENTER + v
```

You should notice that, now, all your terminals will open up vertically, even when you leave out 
the v from your subsequent $MOD + $ENTER commands. Let's try switching back to horizontal mode. Use:

```
$MOD + $ENTER + h
```

Now, let's try switching which terminals are in focus. Use:

```
$MOD + {j,k,l,;}
```

You'll notice that these are the vim keys shifted over to the right by one. This is intentional.
A lot of people, however, myself included, prefer to use the direct vim keys + the modifier key
for changing focus. It's possible to rebind your keys to accomplish this purpose, and we'll do
that in the next section.

Finally, let's try quitting out all of your terminals. Press:

```
$MOD + $SHIFT + q
```

This will exit out of the window currently in focus. Do so repeatedly until all your windows close.

In the next section, we'll explore the i3 configuration file, so we can edit both how it
looks and what keys we use to perform actions. For a more detailed list of what keybindings you
can use, go here: https://i3wm.org/docs/userguide.html#_default_keybindings/

## Editing the i3 Config File

The next step is to edit the i3 configuration file. We're going to do things:
* Swap out {j, k, l, ;} movement keys with {h, j, k, l}
* Remove the default i3status bar (because it's ugly)

Open **.config/i3/config** in vim and find the section with these commands:

```
# change focus
bindsym $mod+j focus left
bindsym $mod+k focus down
bindsym $mod+l focus up
bindsym $mod+semicolon focus right
```

Change this to:

```
# change focus
bindsym $mod+h focus left
bindsym $mod+j focus down
bindsym $mod+k focus up
bindsym $mod+l focus right
```

Next, scroll down and find the section with these commands:

```
# move focused window
bindsym $mod+Shift+j move left
bindsym $mod+Shift+k move down
bindsym $mod+Shift+l move up
bindsym $mod+Shift+semicolon move right
```

Change this to:

```
# move focused window
bindsym $mod+Shift+h move left
bindsym $mod+Shift+j move down
bindsym $mod+Shift+k move up
bindsym $mod+Shift+l move right
```

Lastly, scroll down even more to find this section:

```
bar {
    status_command i3status
}
```

Comment this out, so you would get:

```
# bar {
#     status_command i3status
# }
```

Press the following keys to refresh i3:

```
$MOD + $SHIFT + r
```

i3 will now use your updated keybindings. Additionally, the status bar will have been disabled.
Exit out of vim now. We'll come back to **.config/i3/config** in the near future to enable polybar.

## Configuring and Enabling Polybar



## Kernel Compilation

This last step is intended to teach you about how Arch Linux handles kernel compilation.

The Arch Build System (ABS) is a system for taking source code and compiling it into
binary packages that `yay` or `pacman` can install. Most packages will provide a PKGBUILD
file that specifies how their source code is compiled into a package (similar to a Makefile).
For packages in the official Arch Linux repositories, you can find Git repositories containing
their PKGBUILDs using a tool called `asp`. We're going to look at the Git repository for Arch's
**linux** package, which, as you might imagine, contains the Linux PKGBUILD.

First, run:

```
asp update linux
```

This will cause `asp` to pull the Linux PKGBUILD repository from the Internet and store it
somewhere on your computer (in some file cache).

Now, run:

```
cd ~
asp checkout linux
```

You should see the **linux** directory if you run `ls`. Try running `tree linux`. You should get:

```
linux
├── repos
│   ├── core-i686
│   │   ├── 90-linux.hook
│   │   ├── config.i686
│   │   ├── config.x86_64
│   │   ├── linux.install
│   │   ├── linux.preset
│   │   └── PKGBUILD
│   ├── core-x86_64
│   │   ├── config
│   │   ├── PKGBUILD
│   │   └── sphinx-workaround.patch
│   └── testing-x86_64
│       ├── config
│       ├── PKGBUILD
│       └── sphinx-workaround.patch
└── trunk
    ├── config
    ├── PKGBUILD
    └── sphinx-workaround.patch

5 directories, 15 files
```

There are two main subdirectories of note, **repos** and **trunk**.
1. **repos** contains the PKGBUILD for Linux, separated by assembly language. Arch Linux suports i686 and x86_64, which is why you see core-1686 and core-x86_64. Your machine is running x86_64, so we're going to use **linux/repos/core-x86_64**.
2. The **trunk** is used by developers for staging/testing purposes. It's not super relevant to our needs.

Now, let's inspect the x86_64 PKGBUILD. Run:
```
cd linux/repos/core-x86_64
vim PKGBUILD
```

At the top, you'll see a couple of variables. PKGBUILD's are really just retextured bash
scripts, so all of the syntax is the same. Some variables of note:
* pkgbase=linux <sub>The name of the package</sub>
* pkgver=5.6.11.arch1 <sub>The version of Linux we're using</sub>
* makedepends=(...) <sub>The packages that the Linux package depends on</sub>
* validpgpkeys=(...) <sub>Used to verify the integrity of source files</sub>
* sha256sums=(...) <sub>The checksums for all of the files we're compiling.</sub>

Scrolling down, there are two functions of note, standardized by the PKGBUILD system:
* prepare() { ... } <sub>Applies patches to Linux, generates a default config</sub>
* build() { ... } <sub>Runs Linux Makefile build commands</sub>

Now, let's actually use the PKGBUILD to compile Linux. Inside linux/repos/core-x86_64, run:

```
MAKEFLAGS="-j1" makepkg -sc
```

The `-s` flag tells `makepkg` to install any missing dependencies (syncdeps), and the `-c` flag tells `makepkg` to clean up after producing a valid binary package. The MAKEFLAGS option is passed to the Linux Makefile, and in turn, "-j1" specifies that Linux should use 1 processor in the compilation phase.

Linux takes a long time to compile, and it will take even longer running on a virtual machine with only one thread compiling. The PKGBUILD will also clone the entire Linux source tree (which is huge), so it's probably not going to finish in your lifetime. Exit out using CTRL-C.
