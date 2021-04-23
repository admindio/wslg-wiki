Thank you for your interest in WSLg.

On category of issues that we have seen popping up is folks having trouble getting their GUI application to properly connect to WSLg's X server. This page is meant as a quick guide to diagnose this type of connection issue as well as list the currently known problem we're working on fixing.

# **DISPLAY** environment variable

WSLg's X server is running on display 0. The DISPLAY environment variable must have the value :0 for GUI application to connect to the right display. You can verify what the value of your DISPLAY environment variable is per below.

```
spronovo@OFFICE:~$ echo $DISPLAY
:0
```

This environment variable is initialize as part of WSL's INIT. If it is unset or has a value other than :0, than you likely have a profile script that is changing it's value that you'll want to hunt down. You can also reset that environment variable like below.

```
export DISPLAY=:0
```

# X11 display socket

X servers create their socket under /tmp/.X11-Unix. This directory must exist and must be linked to /mnt/wslg/.X11-Unix where WSLg built-in X server create it's socket. You can verify the mapping exist and is the expected link per below.

```
spronovo@OFFICE:~$ ls -la /tmp/.X11-unix
lrwxrwxrwx 1 spronovo spronovo 19 Apr 21 15:28 /tmp/.X11-unix -> /mnt/wslg/.X11-unix
```

This link is setup during WSL's INIT. If this directory doesn't exist, something likely caused it be removed in your environment that needs to be tracked down.  

You can re-create the link manually to try things out.

```
ln -s /mnt/wslg/.X11-unix /tmp/.X11-unix
```

# X11 server running?

If the X server is running, you should see an X0 socket

```
spronovo@OFFICE:~$ ls /tmp/.X11-unix
X0
```

If you don't please open an issue and attach /mnt/wslg/weston.log to the bug.

# Known issues

You can verify the version of WSLg you are running per below:

```
spronovo@OFFICE:~$ cat /mnt/wslg/versions.txt
WSLg ( x86_64 ): 1.0.17+3.Branch.master.Sha.a526dfd5ad03d126bb2d8c528f6c3563e86a40da
Mariner: VERSION="1.0.20210224"
FreeRDP: e4a2fc2053bd8c5f99455fcd08ffee7e5591567a
weston: fd961f5cd116c9358d82ce94d139c1578e21bd00
pulseaudio: 2f0f0b8c3872780f15e275fc12899f4564f01bd5
mesa:
```

## Complex monitor arrangement
There is a known issue in WSLg 1.0.17 that if you have a combination of vertically and horizontally aligned monitor, Weston may hit an invalid assert and restart. Effectively crashing and restarting the X server on every connection attempt.

You can verify if this is what you are hitting per below

```
cat /mnt/wslg/weston.log | grep isConnected_V
```

if you see something like

```
weston: ../libweston/backend-rdp/rdpdisp.c:481: disp_monitor_validate_and_compute_layout: Assertion `isConnected_V == true' failed.
```

Then you are hitting this problem. The workaround at the moment is to stack all of your monitor either vertically, or horizontally, but not use a mix of both.

## Setting /tmp in /etc/fstab

There is a known issue at the moment (https://github.com/microsoft/wslg/issues/43) where configuring /tmp in /etc/fstab will overwrite the /tmp/.X11-unix link previously described. The workaround at the moment is to either avoid configuring /tmp, or manually recreating the link

```
ln -s /mnt/wslg/.X11-unix /tmp/.X11-unix
```

# Still having a problem?

Please open an issue and attach your /mnt/wslg/weston.log file.