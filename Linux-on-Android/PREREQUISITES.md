# ***WARNING: ⚠***
**DO ALL OF THIS AT YOUR OWN RISK!!**
LIST OF ALL THE WARNINGS:
* **Rooting** may lead to soft bricking your device try it at your own risk.
* **Disabling dm-0 verity: **
* You **must** know how to recover your device in case something breaks.
* If you brick your device, that's on **you**.
* The following steps modifies the system attempt at your own risk. 
* This guide does ***NOT*** cover steps required to repair your device or any additional documentations..
* **These steps will modify your system partition**, which may break OTA updates and require manual system restoration.
* **This method is experimental**, and unexpected behavior may occur. **Doing this on an older devices is recommended..**
* **Running glibc binaries on Android is unsupported** expect breakage, instability, or security risks.
* **OPTIONALLY:** **Do not attempt this on a production device.** Use a test device or an old phone you don't care about.
* **DO NOT ASK FOR SUPPORT IF YOU BREAK YOUR DEVICE.** You have been warned.
---
---
# Prerequisites:
* **A rooted device.**
* **/system partition *being* read-write is best. (This does cover if /system is not read write).**
* **Dm-0 verity and AVB 2.0 must be disabled..**
* **Some good amount of space in /data to store a Linux rootfs**
* **Disable seLinux either through `/proc/cmdline` or by running `setenforce 0`**
* **You should have a backup. Backup for your device's data In case something does go wrong.**

#### A few common questions:
1. **What if I don't have root?**

You can ***root*** your device using **magisk**, **kerneSU**, or other options that let you **root** your device (attempt at your own risk.)

2. **What if I don't have rw system? Or my device somehow doesn't support rw even if dm-0 verity is off?**
First, if you have disabled dm-0 verity (through magisk or in whatever way) try the following command:
```sh
mount -o remount,rw /
mount -o remount,rw /system
```
And try to create a file in /system
```
touch /system/test
```
If it worked you have a fully rw system..

3. **What if /system partition still refuses to be read-write?**
You can actually **not** make /system read-write and skip over this Though i am not sure of how stable it is. It could be more stable than the original method but if you wanna proceed with this. make sure to unlink /etc and you should be good for the most part the README.md does cover how to do this..
