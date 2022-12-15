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
GRUB_CMDLINE_LINUX="i915.enable_psr=0 rhgb quiet mem_sleep_default=deep"
```

The `i915.enable_psr=0` will fix the flickering.

The `mem_sleep_default=deep` is normal suspend to ram where power is used mainly by the memory and nothing else.

After you modify the `/etc/default/grub`, you have to run
- on Ubuntu - `sudo update-grub`
- on Fedora - `grub2-mkconfig -o /etc/grub2-efi.cfg`

To check the `mem_sleep` do:

```
$ cat /sys/power/mem_sleep
s2idle [deep]
```

The value in `[]` is the default one.

## Open issues

The fingerprint reader doesn't work - no driver for now..

## References:

- [https://wiki.archlinux.org/title/intel_graphics#Screen_flickering](https://wiki.archlinux.org/title/intel_graphics#Screen_flickering)
- [https://www.kernel.org/doc/Documentation/power/states.txt](https://www.kernel.org/doc/Documentation/power/states.txt)
- [https://www.youtube.com/watch?v=OHKKcd3sx2c](https://www.youtube.com/watch?v=OHKKcd3sx2c)
