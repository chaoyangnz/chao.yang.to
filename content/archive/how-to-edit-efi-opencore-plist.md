---
title: How to edit EFI OpenCore plist
date: 2020-09-05T02:04:34.816Z
description: How to edit EFI OpenCore plist
---
Recently, I plan to make my gaming PC (Intel NUC Hades Canyon) as my major workstation. I installed Hac-mini in this PC and it works pretty well. 

Basically, I bought two SSD disks (Sumsung 970 EVO Plus) and one for Windows 10 and another for MacOSX. The installation is pretty simple, bake the image and install with USB.

Once I installed both, I patched Mac with OpenCore so that it can boot dual OS. But I found the boot args are not working as my expectation: the default order I hope should be Windows 10 and timeout is too short.

This is what I did to modify these args.

- run these commands in administrative command prompt:

```bash
diskpart
list disk
select disk 0
list partition
select partition 1
assign letter=e
exit
```

Now I mount it to E disk. I have to kill explorer.exe and relaunch explorer from admistrative command so that I can  access the files in EFI disk. If the opencore is installed in the same disk of current OS, you can also mount it with \`mountvol e: /s\` and unmount with \`mountvol e: /d\`.

- download ProperTree from <https://github.com/corpnewt/ProperTree>

- Open E:/EFI/OC/config.plist with ProperTree

- modify the boot args under Misc/boot menu