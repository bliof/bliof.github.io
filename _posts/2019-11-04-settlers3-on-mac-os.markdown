---
layout: post
title: "Settlers 3 on Mac OS"
date: 2019-11-04 23:40:15 +0200
categories: en
tags: [games]
---

## Install the game

### 0. Download the game

From GOG Galaxy -> Library -> Settlers 3 -> Backups & goodies

### 1. Install the game with `wine`

```
wine setup_the_settlers_3_-_ultimate_collection_1.60_v2_(30349).exe
```

The game is installed in `"~/.wine/drive_c/GOG Games/Settlers 3 Ultimate/"`

### 2. Fix the registry

There is a file called `regs.cmd` - `"~/.wine/drive_c/GOG Games/Settlers 3 Ultimate/regs.cmd"`

Remove all the `/reg:32` from all lines.

To apply it - in `"~/.wine/drive_c/GOG Games/Settlers 3 Ultimate/"` just run `wineconsole` and run the script `./regs.cmd`.

### 3. Set up LAN multiplayer

Run `winetricks directplay`

And then use e.g. hamachi to make a local network.

## Create an application with an icon

### Script that starts the game

```
#!/bin/bash

dir="$HOME/.wine/drive_c/GOG Games/Settlers 3 Ultimate"

cmd=$"cd \"$dir\"; /usr/local/bin/wine S3.EXE -log=settlers.log > /dev/null 2>&1"

bash -lc "$cmd"
```

### Icons

There is a nice icon called `goggame-1207659185.ico`. To use it as a image for the application it should be converted to some weird format called `.icns`
Unfortunately it doesn't work just by using `convert` (imagemagic) and one has to go through `png`

0. `convert goggame-1207659185.ico icon` will generate few files
1. `mkdir icons.iconset`
2. `mv icon-0.png icons.iconset/icon_32x32.png` move the files with suffix as the image size
3. `mv icon-2.png icons.iconset/icon_48x48.png` and so on... the rest of the files
4. `iconutil --convert icns icons.iconset/` will create the .icns file

### Create the app

```
platypus -R -i icons.icns settlers3.sh
mv Settlers3.app /Applications/
```

👏 [https://github.com/sveinbjornt/Platypus](https://github.com/sveinbjornt/Platypus)
