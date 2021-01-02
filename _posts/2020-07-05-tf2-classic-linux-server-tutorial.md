---
title:  "TF2 Classic Ubuntu Linux Dedicated Server Guide"
layout: post
---

# Prerequisites

- A Linux server running Ubuntu Server\* on an x86_64 CPU. You cannot run SRCDS on any other architecture besides an x86_64 CPU. Sorry Raspberry Pis. And no, you do not want to host a server using WINE so stop suggesting that. 
- A SFTP/SSH client (PuTTY, Termius, FileZilla, MobaXterm)
- At least 16GB of free storage

This guide was written for and tested on Ubuntu Server 20.04 LTS, however any Debian-based distro should work similarly and fine using this guide.

_\*Ubuntu 20.04 LTS is the first Ubuntu version that starts to phase out many i386 libraries from Focal Fossa repos and future Ubuntu versions. Many Source games still rely on i386 libraries. While TF2C, Steam, and SteamCMD are known to work fine, it is possible other Source games and custom plugins and addons may or may not work properly without utilization of PPAs._

# Creating a steam user for SteamCMD and Source SDK Base 2013 Dedicated Server
Pick a directory to install your server into. Industry standard is usually in `/opt`, but `/home` may be easier for you. We'll pick `/opt/tf2classic`.

Create a user with that home directory and give it a stronk password:
```bash
sudo sudo useradd -m -d /opt/tf2classic -s /usr/bin/bash steam
sudo passwd steam
```
`-m` creates a home directory for it, `-d /opt/tf2classic` specifies where our home directory will be, and `-s /usr/bin/bash` sets the shell to bash.

# Installing Source SDK Base 2013 DS, SteamCMD, and dependencies

The SteamCMD package is in the multiverese repos. TF2C and SteamCMD require i386 libs to function. You also need `p7zip-full` to extract TF2 Classic.
```bash
sudo add-apt-repository multiverse
sudo dpkg --add-architecture i386
sudo apt update
```

Install SteamCMD and p7zip-full:
```bash
sudo apt install steamcmd p7zip-full
```

You will also need the i386 library of `libncurses5` and `libtinfo5`. 
```bash
sudo apt install libncurses5:i386
```

Login to the `steam` user you created: `su - steam`

Run `steamcmd` and let it update. When it's finished updating, login anonymously: `login anonymous`

We will set a directory to install Source SDK Base 2013 DS to.<br>Run `force_install_dir insert_full_path_here` in SteamCMD to select the directory the server install into. Make sure it's in a folder you have access to! Let it be a directory in the `steam`'s home folder. We'll pick `/opt/tf2classic/server`

Then run `app_update 244310 validate` in SteamCMD to install Source SDK Base 2013 DS.

Give it a few, and when it's finished we can `exit`.

# Downloading TF2 Classic
Download TF2 Classic full (**not** the patch) from one of the following websites:
- https://khromier.com/tf2c/tf2classic-2.0.1.7z
- https://gg.apple-shack.org/tf2c/tf2classic-latest.7z

Valid MD5 checksums:
```
A2F240E0136B0E330C91D788CD317AFC *tf2classic-2.0.1.7z
CB9C8F8F732FBF118C1149F8D2A07DAB *tf2classic-latest.7z
```
_(The 7z sizes may appear different. There are no actual data differences. Just different compression and some file name differences. See the SHA256 checkums after extracting both 7z's.)_
![SHA256 checksums for both folders post-extraction.](https://i.imgur.com/i8Hlw3g.png)

You can either download it to your PC and upload it with an SFTP client, or download it on the server using wget or curl. 
```bash
wget https://khromier.com/tf2c/tf2classic-2.0.1.7z
# or
curl -L -O https://khromier.com/tf2c/tf2classic-2.0.1.7z
```
This will take a while depending on your connection and the mirror's load. If you have an unstable internet connection, use the torrent and upload it to your server with SFTP:

`magnet:?xt=urn:btih:2fc7113011ff80f8f05ad8df00b8228aad230117&dn=tf2classic-2.0.1.7z&tr=udp%3a%2f%2ftracker.opentrackr.org%3a1337%2fannounce`

Extract the 7z by running `7z x tf2classic-2.0.1.7zz`. This will be a while depending on your disk speed.

Then finally move the directory into your SDK 2013 MP folder: `mv tf2classic insert_full_path_to_server_directory_here`. 

# Server Configuration
Generate your server config(s) on [cfg.tf](https://cfg.tf/server/).

Make sure the server type is set to "Internet and LAN" if you want players outside your LAN to be able to join _(you may need to port forward if you're on consumer broadband or open ports on your firewall)_.

Upload the generated ZIP file to your server using SFTP and move the `cfg` folder into `tf2classic/cfg`.

# Creating the server script.
### All steps will be executed in the server folder where you downloaded SDK Base 2013 DS to.
Create a script to run the server with one simple command. Use any text editor of your choice to create `runserver.sh`, like nano, where `srcds_run` is located.<br>
`nano runserver.sh`

Fill it with this line: 
```bash
./srcds_run -console -game tf2classic +map pl_upward +maxplayers 24
```

Feel free to change the map and maxplayers. There are more arguments, but we'll keep it basic.

Save the file (Nano users will hit (DE: STRG) CTRL + X) and give the script execute permissions with this command: `chmod +x runserver.sh`

# (important) Create symlinks to missing shared objects.
Valve changed some shared object file names in the SDK and the objects we're given have not adapted to the new names. Since these are simply renames, we can symlink them.<br>
 **Your server will not start without doing this.**

Go into the `bin` directory of the SDK 2013 folder: `cd bin`<br>
_**Not** `/bin`. That's a root directory._

Run the following commands to create the symlinks in the `bin` folder:
```bash
ln -s datacache_srv.so datacache.so
ln -s dedicated_srv.so dedicated.so
ln -s engine_srv.so engine.so
ln -s materialsystem_srv.so materialsystem.so
ln -s replay_srv.so replay.so
ln -s scenefilecache_srv.so scenefilecache.so
ln -s shaderapiempty_srv.so shaderapiempty.so
ln -s studiorender_srv.so studiorender.so
ln -s vphysics_srv.so vphysics.so
ln -s soundemittersystem_srv.so soundemittersystem.so
```

Here's what it should look at like after doing so:
![bin folder after symlinks](https://i.imgur.com/DWPBsCS.png)

# Running the server
Finally, all you need to do to start the server is run `./runserver.sh`!

# FAQ:

## _I still get_
```
WARNING: Failed to load 32-bit libtinfo.so.5 or libncurses.so.5.
Please install (lib32tinfo5 / ncurses-libs.i686 / equivalent) to enable readline
``` 
## _even after installing `libncurses5`!_

You need to install `libncurses5:i386`. You probably installed the `amd64` variant of it. The `:i386` is important.

## _`sudo apt install steamcmd`_ says something about no installation candidate!

You need to enable i386 libraries, the multiverse repo, and refresh your local apt database:
```bash
sudo add-apt-repository multiverse
sudo dpkg --add-architecture i386
sudo apt update
```

## _Why can't other users connect to my server?_

### Two possible reasons:

If you're on consumer broadband, you may need to port forward port 27015 UDP on your router. Because of the vast majority of router models and configurations, port forwarding cannot be documented in this guide. I suggest you Google _"how to port forward on \<your router model\>"_.<br>
To test if you've successfully port forwarded, visit [canyouseeme.org](https://canyouseeme.org) on a separate machine on the same network as your server, enter in port 27015 and see if it's successful.

If you're on a VPS, your provider may do auto configuration and install a pre-configured firewall automatically. Generally speaking they use Uncomplicated Firewall, because it's... uncomplicated.<br>
To open 27015 UDP on this firewall, run: 
```bash
sudo ufw allow 27015/udp
```
This is not needed if you don't have a firewall enabled.

If you intend to use RCON for things like SourceBans, you will need to open a TCP RCON port too. By default they use 27015 TCP. You will need to add launch arguments to enable RCON as well.

You also need to give the player your **WAN** IP after port forwarding or opening ports on your firewall. Not your LAN IP. To get this, run this very easy command: `curl ifconfig.me`<br>
You will give players that IP address to put in their console to connect to. E.g. `connect 42.0.13.337`

## _How do I get SourceMod?_
Unfortunately, installing SourceMod+MetaMod to TF2C isn't as simple as drag and dropping the folders like officially supported games are such as traditional TF2 and CSS. There are far too many extra steps you need to do when you install SourceMod+MetaMod that it warrants a separate guide for. I do intend to write a guide for this in the future.

## _How do I make my server run automatically?_
The default init modern distros use, systemd, supports creating service unit files similarily to how we used to create startup shell scripts, just in a more unified format and even some neat features. There are many types of configurations for these such as using screen/tmux or even an RCON program. But for simplicity we can create a very basic simple unit that spawns the server and nothing more.

I do not suggest you actually use this due to the constraints it has I listed. I suggest you seek something more professional which I did list below.

As an elevated user (either root or go back to your normal user account) create a file named `tf2classic.service` in the following directory: `/usr/lib/systemd/system/`

Fill it up with the following contents:
```ini
[Unit]
Description=Team Fortress 2 Classic Server
RefuseManualStart=no
RefuseManualStop=yes
After=network.target

[Service]
User=steam
Group=steam
Type=simple
WorkingDirectory=/opt/tf2classic/server
ExecStart=/bin/sh /opt/tf2classic/server/runserver.sh
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Replace the `User=` and `Group=` if you made your steam user a different name.<br>
Change the script path in `ExecStart=` if you made your script in a different location. Leave `/bin/sh` in there as it needs a shell to interpret your script.<br>
Change `WorkingDirectory=` to where you installed Source SDK 2013 to if it's different.

To load this, run `sudo systemctl daemon-reload`, and to test it out: `sudo systemctl start tf2classic`.<br>
To enable it so it starts up automatically on reboot: `sudo systemctl enable tf2classic`. This will also automatically restart if in the rare event the server crashes and does not restart like it normally does by default.

Note that this is a very poor and weak man's unit. You cannot access the console in any way and to kill it you must kill the actual script itself since crash detection will think it crashed and automatically restart after 10 seconds. **I absolutely do not suggest anyone uses this.** However it is the most simplest way to make your server start automatically on reboot. Treat this as your introduction to systemd units :)<br>
If you want, you can replace the `ExecStart=` line with the actual start line in your server script.<br>
Example: `ExecStart=/opt/tf2classic/server/srcds_run -console -game tf2classic +map pl_upward +maxplayers 24`

If you're looking for more professional scripts that I even use myself, take a look at this wonderful post on Alliedmodders: https://forums.alliedmods.net/showthread.php?t=273139