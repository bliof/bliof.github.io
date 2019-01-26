---
layout: post
title:  "Steam Client on Mac OS case sensitive issue"
date:   2019-01-26 12:30:00 +0200
categories: en
tags: [oh-my]
---
For some reason the Steam Client won't work on case sensitive file system on Mac OS..

What happens is that you start steam and your screen may flicker and then steam will exit.

You can verify any issues with steam by checking the logs in:

```
~/Library/Application\ Support/Steam/logs/

# and most probably
~/Library/Application\ Support/Steam/logs/bootstrap_log.txt
```

A way to fix the issue is to create a case-insensitive volume and move steam there.

```bash
diskutil apfs addVolume disk1 APFS Steam
mv /Applications/Steam.app /Volumes/Steam/Steam.app
ln -s /Volumes/Steam/Steam.app /Applications/Steam.app
mv ~/Library/Application\ Support/Steam /Volumes/Steam/Library
ln -s /Volumes/Steam/Library ~/Library/Application\ Support/Steam
```

Relates:

- [Solution Described On The Steam Forum 👏](https://steamcommunity.com/discussions/forum/2/1698294337771601383/?ctp=3)
