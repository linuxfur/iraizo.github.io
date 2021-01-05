---
title:  "TF2 Classic SourceMod+Metamod Setup"
layout: post
---

### Note: If you don't have a server up and running, here's a [guide for that](https://iraizo.github.io/tf2-classic-linux-server-tutorial/).

So, as expected, SourceMod isn't necessarily drag-and-drop like supported SM games are such as TF2 and CS:Source due to this simply being a mod built off of Source SDK 2013 with things like a different game name (`tf2classic`), offsets, signatures, etc.

While the process of getting SM to work isn't that hard, the pins in the [#servers](https://discord.com/channels/196337717267791874/666971024960651284) channel of the [TF2 Classic Discord](https://discord.gg/3zMk4vn) aren't that great either. Information is scattered, there's outdated information, and no one likes to read pins (sadly). This is meant to unify it a bit.

<hr>

## Getting SourceMod and MetaMod

Visit the Stable builds section of [sourcemod.net](https://www.sourcemod.net/downloads.php?branch=stable) and grab the latest Linux build. Download it to your server using wget/curl or to your machine and upload it to your server using SFTP.

```bash
curl -L -O https://sm.alliedmods.net/smdrop/1.10/sourcemod-1.10.0-git6502-linux.tar.gz
# or
wget https://sm.alliedmods.net/smdrop/1.10/sourcemod-1.10.0-git6502-linux.tar.gz
```

##### This link may be outdated and will not be updated. Please get the direct link of the latest build yourself and replace the link.

You'll also need stable Metamod:Source from [here](https://www.sourcemm.net/downloads.php?branch=stable). Download it with one of the ways you did.

Make sure the archives are in your `tf2classic` game folder.<br>
(`location_of_sdk2013ds/tf2classic`)

Extract it in that location:
```bash
tar -xf sourcemod-1.10.0-git6502-linux.tar.gz
tar -xf mmsource-1.11.0-git1144-linux.tar.gz
```

Feel free to remove the archives. 

There are a few things we need to do before launching the server.

## Replacing `metamod.vdf`

You should see an `addons` folder at the end. We need to replace a file in there.<br>
![Addons folder after extracting SM and MM](https://i.imgur.com/E299URP.png)

`metamod.vdf` is necessary for your server to recognize the game. Since this is a mod, we need to use a web tool to generate a metamod.vdf file for us that points to `tf2classic`.

Go into your `addons` folder in your `tf2classic` server and delete `metamod.vdf`. No need to back it up or anything.

Head over to [sourcemm.net/vdf](https://www.sourcemm.net/vdf).<br>
You're going to leave the "Game:" blank, or as "---". In the "Game Folder" box, enter `tf2classic`. It should look *exactly* like this.<br>
![Metamod generator page example](https://i.imgur.com/SzhcduM.png)

Download this file and transfer it to your server via SFTP. Move it to your `addons` folder.<br>
![Addons folder after adding the new metamod.vdf](https://i.imgur.com/G0HZhiF.png)

<hr>

### Great, we're half way done c;

<hr>

## Importing custom gamedata

To get things like the showmenu, radio menu, hud message, etc to work, we need to import custom gamedata. Do not edit `common.games.txt` anymore as this is not reliable and a hacky solution. It also requires disabling auto updating. The official method is to create data in `custom` subdirectories.

If you're using Ubuntu Server, you might have the `git` program on your server. If you do, move to the `tf2classic` directory and clone the following repo with this command:
```bash
git clone https://github.com/Scags/TF2Classic-Tools.git
```

If you don't, download this repo with wget/curl directly:
```bash
wget https://github.com/Scags/TF2Classic-Tools/archive/master.zip
# or
curl -L -O https://github.com/Scags/TF2Classic-Tools/archive/master.zip
# then unzip it
unzip master.zip
# if you do not have unzip, install it using: sudo apt install unzip
```

`cd` into the directory, being either `TF2Classic-Tools` or `TF2Classic-Tools-master` depending on how you downloaded it.

The `sourcemod` directory is what we're after. Run the following command to copy it to your `addons` folder:
```bash
cp -r sourcemod/ ../addons/
```

Now we have the necessary gamedata for plugins and core SM and MM functions to work with TF2C! One more thing c:

## Setting yourself as the "root" admin user.

You're gonna need to be able to do administrative actions, but SourceMod doesn't know that yet! Thankfully, it's pretty simple.

First, grab your SteamID using a website like [steamid.io](https://steamid.io). Simply enter your custom URL or your steamID64. Take note of the `steamID` line.<br>
![Example Steam lookup](https://i.imgur.com/46H40oA.png)

Open the file `tf2classic/addons/sourcemod/configs/admins_simple.ini` with a text editor.<br>
At the very bottom, create a new line following this scheme:
```
"steamID" "99:z"
```
`99:z` means _all_ permissions available. Only gives this permission to server operators as you can do more than you realize with this kind of power.

For example, here's my `admins_simple.ini`:<br>
![Example admins_simple.ini](https://i.imgur.com/Cc9EZZL.png)

To assign lesser admins, you can follow the same scheme with the help of the comments in this file and the wiki: [https://wiki.alliedmods.net/Adding_Admins_(SourceMod)](https://wiki.alliedmods.net/Adding_Admins_(SourceMod))

Save the file, then start your server up and have fun!

<hr>

### Note: While I tested map voting, you should not need to delete `nextmap.smx`. If you have issues with map voting, try disabling or removing the plugin. There are other map voting plugins out there too!

<hr>

### You might get an error about missing dhooks.ext. It does seem safe to ignore **if you don't have any plugins that need dhooks**, but if you use something like Scags' TF2C Crash Exploit Fixes you will need DHooks w/Detours.

To install DHooks with Detours, download this ZIP file and upload it to your server: https://forums.alliedmods.net/attachment.php?attachmentid=184479&d=1602926778<br>
(If you need pre-SourceMod v1.10 build of DHooks w/Detours for some reason: https://forums.alliedmods.net/attachment.php?attachmentid=184480&d=1602926778)

##### Original post: https://forums.alliedmods.net/showpost.php?p=2588686&postcount=589

Extract it, and move the `addons` folder to `tf2classic` and restart the server.

<hr>

##### Written by [linuxfur](https://twitter.com/linuxfur) \<3