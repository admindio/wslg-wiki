Various debugging options for WSLg can be configured by editing the file ```c:\ProgramData\Microsoft\WSL\.wslgconfig```. This config file is read during WSLg launch, so any change require WSLg to be restarted for the changes to take effect (```wsl --shutdown```).
 
```
[system-distro-env]
;
;enable weston-debug for live debug view message
;weston-debug -l to see available scopes
WESTON_DEBUG_PROTOCOL=true
;
;enable performance timeline
WESTON_LOG_SCOPES=timeline
;
;enable Xwayland debug
;WESTON_LOG_SCOPES=xwm-wm-x11
;
;rdp-backend debug level
;level 0 to 5
WESTON_RDP_DEBUG_LEVEL=5
;
;rdprail-shell debug level
;level 0 to 5
WESTON_RDPRAIL_SHELL_DEBUG_LEVEL=5
;
;clipboard debugging
WESTON_RDP_DISABLE_CLIPBOARD=true
WESTON_LOG_SCOPES=rdp-backend-clipboard
;level 0 to 5
WESTON_RDP_DEBUG_CLIPBOARD_LEVEL=5
;
;monitor refresh rate
;60 or 144
WESTON_RDP_MONITOR_REFRESH_RATE=144
;
;shared memory
WESTON_RDP_DISABLE_SHARED_MEMORY=true
;
;window z-order sync with Windows
WESTON_RDP_DISABLE_WINDOW_ZORDER_SYNC=true
;
;window title
WESTON_RDP_DISABLE_APPEND_DISTRONAME_TITLE=true
WESTON_RDP_DISABLE_COPY_WARNING_TITLE=true
;
;start menu with distro name
WESTON_RDPRAIL_SHELL_DISABLE_APPEND_DISTRONAME_STARTMENU=true
;
;overlay icon at start menu
WESTON_RDPRAIL_SHELL_DISABLE_BLEND_OVERLAY_ICON_APPLIST=true
;
;overlay icon at taskbar
WESTON_RDPRAIL_SHELL_DISABLE_BLEND_OVERLAY_ICON_TASKBAR=true
;
;change weston shell
;rdprail-shell (RAIL window remoting) or desktop-shell (Linux full-desktop remoting)
;for desktop-shell, make sure to remove remoteapplication from weston.rdp.
WSL2_WESTON_SHELL_OVERRIDE=desktop-shell
;
;default icon path
WSL2_DEFAULT_APP_ICON=/usr/share/icons/wsl/linux.png
WSL2_DEFAULT_APP_OVERLAY_ICON=/usr/share/icons/wsl/linux.png
WSL2_DEFAULT_APP_OVERLAY_ICON=disabled
;
;hi-dpi
WESTON_RDP_DISABLE_HI_DPI_SCALING=false
WESTON_RDP_DISABLE_FRACTIONAL_HI_DPI_SCALING=false
;100 to 500
WESTON_RDP_DEBUG_DESKTOP_SCALING_FACTOR=100
;
;audio in/out
WESTON_RDP_DISABLE_AUDIO_PLAYBACK=true
WESTON_RDP_DISABLE_AUDIO_CAPTURE=true
WESTON_RDP_DISABLE_AUDIO_PLAYBACK_DYNAMIC_VIRTUAL_CHANNEL=true
;
;Logging Level for Pulseaudio
;0 to 4
PULSE_LOG=4
;
;Logging level for FreeRDP
;TRACE, DEBUG, INFO, WARN, ERROR, FATAL, OFF
WLOG_LEVEL=DBG
;
;xkb debugging
;debug, info, warning, error, critical
XKB_LOG_LEVEL=debug
;
;xkb compiler
;0 to 10
XKB_LOG_VERBOSITY=10
```