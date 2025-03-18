#### **Networking Issues:**
1. If you get networking issues like `apt update` doesn't work and results in a temporary failure.. Run the following.
```bash
echo "nameserver 8.8.8.8" > /etc/resolv.conf
```
2. If you still have networking issues perhaps `apt update` doesn't work or `/bin/ping 1.1.1.1` doesn't work. Try the following.
```bash
umount /sbin
/sbin/su -c "/bin/ping 1.1.1.1" # or apt
# a better way would be to switch into bash with the `su` binary
/sbin/su -c "/bin/bash"
# try ping again
ping 1.1.1.1 # this should work fine same with apt
apt update
```
#### **Certain commands not working:**
If certain commands like `reboot` or something stop failing and give an error that says something along the lines of
```
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
```
that is because it is trying to run systemd related commands on android and to run whatever binary that was failing specify its full path like for `reboot` do `/system/bin/reboot`..
If sudo command fails with some suid issue run the following:
```sh
/usr/bin/su 
apt install --reinstall sudo
busybox mount -o remount,dev,suid /usr # if u have busybox
# otherwise try mount
mount -o remount,dev,suid /usr
# add yourself or user to /etc/sudoers
