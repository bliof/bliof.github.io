---
layout: post
title: "Linux And Matebook X Pro (MRGF-X)"
date: 2022-12-15 23:00:33 +0200
categories: en
tags: [notes, linux]
---

## Configuring The Laptop

When you install some linux distro on the Hauwei Matebook X Pro 2022 (MRGF-X), you'll hit multiple issues with the default setup.

1. There will be screen flickering / lagging. Overall it feels like the display renders / shows the state from few seconds ago.
2. The laptop will run out of battery from time to time when on sleep for no reason.

To solve them, specify in `/etc/default/grub` this:

```
GRUB_CMDLINE_LINUX="i915.enable_psr=0 rhgb quiet"
```

The `i915.enable_psr=0` will fix the flickering.

After you modify the `/etc/default/grub`, you have to run
- on Ubuntu - `sudo update-grub`
- on Fedora - `grub2-mkconfig -o /etc/grub2-efi.cfg`

## Open issues

### fingerprint reader

The fingerprint reader doesn't work - no driver for now..

### suspend is broken

Both `s2idle` and `deep` suspend seem to be broken..

`s2idle` issues:
- wastes a lot of power
- doesn't always work - sometimes it fails to wake up normally:
    - it looks like a clean start
    - all apps crash with permission errors
    - it seems like the laptop is running with some weird user

`deep` issues:
- there is no way to wake the laptop :D


#### changing suspend type

Using `mem_sleep_default=deep` in the `GRUB_CMDLINE_LINUX` will force one of the two suspends

To check the active default do:

```
$ cat /sys/power/mem_sleep
s2idle [deep]
```

The value in `[]` is the default one.

## References:

- [https://wiki.archlinux.org/title/intel_graphics#Screen_flickering](https://wiki.archlinux.org/title/intel_graphics#Screen_flickering)
- [https://www.kernel.org/doc/Documentation/power/states.txt](https://www.kernel.org/doc/Documentation/power/states.txt)
- [https://www.youtube.com/watch?v=OHKKcd3sx2c](https://www.youtube.com/watch?v=OHKKcd3sx2c)
