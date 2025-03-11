1. The $PATH resets after every reboot and you have to re ran the setup_linux command..
2. If seLinux isn't disabled in cmdline you will need to run `setenforce 0` every time. **Consider adding this to the setup script.**
3. If the setup_script is not placed /system/bin /system/xbin or a system partition it will be removed after a reboot. (**This is why disabling dm-0 verity for a rw system is recommended**)
4. remounting the system as rw will get very repetitive consider adding `mount -o remount,rw / && mount -o remount,rw /system` into the script.
5. unlinking the directories like /etc /bin can also get pretty annoying so consider adding that to the script.
6. cgroups or namespaces do not magically get fixed by these methods nor does something cool like docker or systemd work.. (For docker systemd is required and same case with systemd.)
7. Permission denied issues are annoying especially when you are already running a root shell and having to run `su -c "your_command"` is annoying.
