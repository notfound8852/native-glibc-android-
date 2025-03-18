
Before starting **YOU MUST READ THE PRE-REQUISISTES FILE..**
# ***Warning:***
DO ALL OF THIS AT YOUR OWN RISK!!
LIST OF ALL THE WARNINGS:
* If you screw up, it's ***your*** fault.
* You **must** know how to recover your device in case something breaks.
* If you brick your device, that’s on **you**.
* The following steps modifies the system attempt at your own risk..
* **DO NOT USE ANYTHING RELATED TO SYSTEMD IT WILL BREAK APT..**  
* This guide does ***NOT*** cover steps required to repair your device or any additional documentations..
* **These steps will modify your system partition**, which may break OTA updates and require manual system restoration.
* **This method is experimental**, and unexpected behavior may occur.
* **Running glibc binaries on Android is unsupported especially directly/natively** expect breakage, instability, or security risks.
* **OPTIONALLY:** **Do not attempt this on a production device.** Use a test device or an old phone you don’t care about.
* **DO NOT ASK FOR SUPPORT IF YOU BREAK YOUR DEVICE.** You have been warned.
---
---

## **GLIBC on Android:**
---

First step or rather the "Prerequisite Step" is going to be selecting the rootfs
**Note:** I will be using ubuntu since it is very stable feel free to choose any rootfs at your own risk of course :)

**Note:** You can use termux or normal system shell as long as commands like curl exist on your system.. If you plan on using termux please run the following:
```sh
pkg install tsu curl -y
```
This ensure you have the `sudo` command for any permission denied errors.
You will eventually need to switch user to root when messing with this.. it can be done by `su` command.
### ***Ubuntu Rootfs:***
--- 
---
#### First step:

make a folder in `/data`call it rootfs.
```sh
mkdir /data/rootfs
cd /data/rootfs
```

---
#### Second Step:

**For arm64:**
```sh
curl https://cdimage.ubuntu.com/ubuntu-base/releases/24.10/release/ubuntu-base-24.10-base-arm64.tar.gz --output ubuntu-base.tar.gz
```
**For amd64:**
```sh
curl https://cdimage.ubuntu.com/ubuntu-base/releases/24.10/release/ubuntu-base-24.10-base-amd64.tar.gz --output ubuntu-base.tar.gz
```

More rootfs are available at **[this link](https://cdimage.ubuntu.com/ubuntu-base/releases/24.10/release/).** You can find one for your appropriate device. You can also use an older rootfs like this **[one](https://cdimage.ubuntu.com/ubuntu-base/releases/22.04/release/)**.
**Note: It is much much more stable..**

---
#### Third Step:
extract the rootfs..
```sh
tar -xf ubuntu-base.tar.gz
```
afterwards switch to system shell by running...
```sh
su
```

---
#### Forth Step:
If selinux is not disabled, disable it temporarily..

### To check:

To check if selinux is enabled run:
```sh
getenforce
```
If it returns "enforcing" then selinux is enabled. If it returns "permissive" you are good to go and can skip this step.

### Disabling:

**Note:** If you have not modified your device in anyway related to selinux it will be 'on' (set to 1) and therefore needs to be turned off for this you need to run:
```sh
setenforce 0
```
Please note that this is ***TEMPORARY***. 

For a permanent solution consider disabling selinux through `/proc/cmdline`. This guide does not cover that but a quick overview is you need a linux machine it can be a vm you need the boot.img of your android device. Extract the boot.img (or open it up whatever you like to call it) and then, you need to add `androidboot.selinux=permissive` when closing the boot.img as a cmdline arg.

---
#### Fifth Step:
Remount the system as read-write
```sh
mount -o remount,rw /
mount -o remount,rw /system
```

---
#### Sixth Step:
Check if /bin exists if it does it is most likely a link to /system/bin remove the link using unlink command
```sh
unlink /bin
```
sanity check
```sh
ls /bin
# should result in 
ls: cannot access '/bin': No such file or directory
```

---
#### Seventh Step:
This is copying over all of android's binaries and files over to the linux rootfs. We do this so that android's init or the linker still has all the binaries it needs and in turn doesn't bother us.
```sh
cp -r /sbin/* /data/rootfs/sbin/
cp -r /lib/* /data/rootfs/lib/
cp -r /root/* /data/rootfs/root/
```

Now /etc is a bit different since it is a symbolic link to /system/etc. 

---
**OPTIONALLY:** You can simply unlink it and not have to worry about /etc entirely.. This can be done by like this:
```sh
rm -rf /lib # if you are using the latest rootfs. But if you are using 22.04 don't do this.
unlink /etc
mkdir /etc
```
you can include the `mkdir /etc` in the later provided script.. I am not too sure about the stability of this though..

---
---
**Directly (Prefered method though a bit finicky):**
But if you don't want to do that you can check for the overlapping files (these will be passwd, group, host). You can simply merge these. 
```sh
cat /system/etc/hosts /data/rootfs/etc/hosts > ./new_hosts
cat /system/etc/group /data/rootfs/etc/group > ./new_group
cat /system/etc/passwd /data/rootfs/etc/passwd > ./new_passwd
```
The other files like ethertypes dont really matter too much
Lastly You can copy everything
```sh
cp -r /system/etc/* /data/rootfs/etc
```
rename and place these:
```sh
mv ./new_passwd /data/rootfs/etc/passwd
mv ./new_group /data/rootfs/etc/group
mv ./new_hosts /data/rootfs/etc/hosts
```

---
#### Eighth Step:
Create a file call it `setup_linux` Open it in a text editor like vim from system shell or nano from termux. 
Paste the following and **DO NOT RUN IT YET**:
**(Note: If you are running the 22.04 ubuntu rootfs you can use mount --bind $ROOTFS/lib /lib same thing for /lib64)**

**for amd64:**
```sh
#!/system/bin/sh

ROOTFS="/data/rootfs"

mkdir /bin /sbin /boot /home /media  /opt  /run  /srv  /tmp  /usr  /var /boot /root
mount --bind $ROOTFS/etc /system/etc
mount --bind $ROOTFS/usr /usr
mount --bind $ROOTFS/boot /boot
mount --bind $ROOTFS/media /media
mount --bind $ROOTFS/opt /opt
mount --bind $ROOTFS/run /run
mount --bind $ROOTFS/srv /srv
mount --bind $ROOTFS/tmp /tmp
mount --bind $ROOTFS/var /var
mount --bind $ROOTFS/root /root
mount --bind $ROOTFS/media /media
ln -s /usr/lib /lib
ln -s /usr/lib64 /lib64
mount --bind $ROOTFS/bin /bin
mount --bind $ROOTFS/sbin /sbin
```
---

**for arm64:**
```sh
#!/system/bin/sh
ROOTFS="/data/rootfs"
rm -rf /lib

mkdir /bin /sbin /boot /home /media  /opt  /run  /srv  /tmp  /usr  /var /boot /root
mount --bind $ROOTFS/etc /system/etc
mount --bind $ROOTFS/media /media
mount --bind $ROOTFS/usr /usr
mount --bind $ROOTFS/boot /boot
mount --bind $ROOTFS/media /media
mount --bind $ROOTFS/opt /opt
mount --bind $ROOTFS/run /run
mount --bind $ROOTFS/srv /srv
mount --bind $ROOTFS/tmp /tmp
mount --bind $ROOTFS/var /var
mount --bind $ROOTFS/root /root
ln -s /usr/lib /lib
ln -s /usr/lib64 /lib64
mount --bind $ROOTFS/bin /bin
mount --bind $ROOTFS/sbin /sbin
```
---

**Quick explanation:** the libraries need to be linked in this way rather than mount binding them this is because apt upgrade will complain about /usr not being merged.. 
I am leaving out /bin and /sbin from being being links this is mainly because of stability issue. But it is possible to do..

**Quick warning:** If you are using magisk and the magisk binaries are in /sbin (in most case they are). Mounting another /sbin over /sbin can cause some issues with the root on the device breaking.. So make sure to revert back to normal after changes or you can just unmount /sbin after running the script
```sh
umount /sbin
```

##### Set the permissions:
```sh
chmod +x ./setup_linux
```
**Optional:** You can go place this in /system/bin or /system/xbin or whatever `bin` folder you find convenient..

---
#### Nineth step:
Run the script.. And then run this:
```sh
export PATH=/usr/sbin:/usr/bin:$PATH
```

#### Verify:
```sh
bash
```
---
---
If there are any issues please refer to the Issues.md file. **[Here](https://github.com/notfound8852/native-glibc-android-/blob/main/Linux-on-Android/Issues.md)**
For convenience here is a quick setup **[guide](https://github.com/notfound8852/native-glibc-android-/blob/main/Linux-on-Android/more/Quick%20setup.md)**

and for the love of God if you plan on using apt like a sane human being please make sure to run the following:
```sh
groupadd -g 3003 aid_inet
groupadd -g 3004 aid_net_raw
groupadd -g 1003 aid_graphics
usermod -g 3003 -G 3003,3004 -a _apt
usermod -G 3003 -a root
groupadd storage
groupadd wheel
```

***Enjoy!***
