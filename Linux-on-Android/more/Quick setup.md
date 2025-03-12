After following the **main guide**. You can run the following to setup the Linux environment.
```sh
groupadd -g 3003 aid_inet
groupadd -g 3004 aid_net_raw
groupadd -g 1003 aid_graphics
usermod -g 3003 -G 3003,3004 -a _apt
usermod -G 3003 -a root
echo "nameserver 8.8.8.8" > /etc/resolv.conf

```

If you used magisk to root your device and the magisk binary is in /sbin run the following:
```sh
umount /sbin
/sbin/su -c "/bin/bash"
mount --bind /data/rootfs/sbin /sbin

```
```bash
apt update
apt upgrade -y
# done
```

You can see the limitations **[here.](https://github.com/notfound8852/native-glibc-android-/tree/main/Linux-on-Android/more/Limitations.md)**
