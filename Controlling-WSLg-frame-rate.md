When running applications through WSLg, the frame rate observed by those applications within Linux will not always match the frame rate observed by the user observing those applications through the Windows desktop. This can be a bit confusing and is the result of WSLg running cascaded compositor: Weston/Wayland in Linux and DWM in Windows. The purpose of this article is to demystify what is going on here as well as explain the level of control available today to choose the rate at which WSLg presents to Windows.

By default WSLg will present up to 60fps to the Windows compositor. So even if an application within WSLg renders at say 500fps within the Linux environment, the Windows host will only be notified for 60 of those frames by default. 

We plan to eventually have WSLg automatically adapt to the Windows compositor clock such that the perfect number of frames is always presented to the host. For example up to 60fps for a 60hz desktop, up to 144fps for a 144hz desktop, etc...

In the interim, the following entry can be used to manually select a presentation rate for WSLg. This setting can be set in ```C:\ProgramData\Microsoft\WSL\.wslgconfig```. For example the following will tell WSLg to present up to 100fps to the Windows host. 

```
[system-distro-env]
WESTON_RDP_MONITOR_REFRESH_RATE=100
```

It is possible to run WSLg at higher rate than the underlying desktop. For example, setting WSLg to present up to 144fps on a 60hz desktop will work, but this is wasteful and not recommended. A 60hz desktop can only consume and present 60fps from any individual application. Any frames presented above 60fps, on a 60hz desktop, will essentially be skipped over and discarded by the Windows compositor. This isn't specific to WSLg applications. Even a native windowed application presenting a 500fps will see 60 of its frame being consumed and presented by the Windows compositor while the remaining frames will be skipped as the monitor can't effectively display them.

Notifying the host of a frame has a CPU and GPU cost, those skipped and discarded frame basically just end up wasting resources. So while presenting too many frame will work, ideally you don't want to present more frame than the Windows compositor can consume as those extra frames are not visible anyway and waste resources.

Ideally you want to set this monitor refresh to match your Windows compositor clock. Generally, the Windows compositor clock is the refresh rate of your slowest monitor. For example, if you are running 3x 144hz monitors... the compositor clock would be 144hz and applications can present up to 144fps... but if you are running a mix of 144hz and 60hz monitors, the compositor clock will effectively be 60hz and windowed applications will be effectively limited to 60fps.

Note that today there is an effective maximum presentation rate for Weston of 144fps... so setting value above 144 will cap at 144.

