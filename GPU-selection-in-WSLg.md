This article describes the GPU selection logic within MESA when running against the d3d12 backend used within WSL/WSLg. The below only apply to MESA and not to other APIs such as CUDA, L0, OpenVINO, etc...

All of the changes described here have been upstream to MESA a while back. Make sure to update/upgrade your distro to pick up the latest set of changes. Depending on which distro you are using and when it last picked up update to the MESA libraries, not all of these changes may be available yet for your distro. 

The original implementation of the d3d12 backend for MESA was picking the very first GPU enumerated as the GPU it will use without user control. The MESA utility ```glxinfo``` can be used to determine which GPU is currently used. For example, below the Intel integrated GPU is being used.

```
spronovo@DESKTOP-4RE4J3H:~$ sudo apt install mesa-utils

spronovo@DESKTOP-4RE4J3H:~$ glxinfo -B
name of display: :0
display: :0  screen: 0
direct rendering: Yes
Extended renderer info (GLX_MESA_query_renderer):
    Vendor: Microsoft Corporation (0xffffffff)
    Device: D3D12 (Intel(R) Iris(R) Xe Graphics) (0xffffffff)
    Version: 22.0.5
    Accelerated: yes
    Video memory: 16429MB
    Unified memory: yes
    Preferred profile: core (0x1)
    Max core profile version: 3.3
    Max compat profile version: 3.3
    Max GLES1 profile version: 1.1
    Max GLES[23] profile version: 3.0
OpenGL vendor string: Microsoft Corporation
OpenGL renderer string: D3D12 (Intel(R) Iris(R) Xe Graphics)
OpenGL core profile version string: 3.3 (Core Profile) Mesa 22.0.5
OpenGL core profile shading language version string: 3.30
OpenGL core profile context flags: (none)
OpenGL core profile profile mask: core profile

OpenGL version string: 3.3 (Compatibility Profile) Mesa 22.0.5
OpenGL shading language version string: 3.30
OpenGL context flags: (none)
OpenGL profile mask: compatibility profile

OpenGL ES profile version string: OpenGL ES 3.0 Mesa 22.0.5
OpenGL ES profile shading language version string: OpenGL ES GLSL ES 3.00
```

User control was [added](https://gitlab.freedesktop.org/mesa/mesa/-/merge_requests/10710) where a user can select the GPU to be used by the d3d12 MESA backend by setting the ```MESA_D3D12_DEFAULT_ADAPTER_NAME``` environment variable. The d3d12 backend will do a case-insenstive substring match and the first adapter enumerated that matches the substring specified in the environment variable will be selected.

The precise name of the various GPU is available in the Windows Device Manager. For example, on a particular Surface Laptop Studio the following GPUs are available.

1. Intel(R) Iris(R) Xe Graphics
2. NVIDIA GeForce RTX 3050 Ti Laptop GPU

The NVIDIA GPU can be selected in WSL by setting ```MESA_D3D12_DEFAULT_ADAPTER_NAME``` to any substring of the string ```NVIDIA GeForce RTX 3050 Ti Laptop GPU```, for example in the following MESA now uses the NVIDIA GPU.

```
spronovo@DESKTOP-4RE4J3H:~$ export MESA_D3D12_DEFAULT_ADAPTER_NAME=NVIDIA
spronovo@DESKTOP-4RE4J3H:~$ glxinfo -B
name of display: :0
display: :0  screen: 0
direct rendering: Yes
Extended renderer info (GLX_MESA_query_renderer):
    Vendor: Microsoft Corporation (0xffffffff)
    Device: D3D12 (NVIDIA GeForce RTX 3050 Ti Laptop GPU) (0xffffffff)
    Version: 22.0.5
    Accelerated: yes
    Video memory: 20279MB
    Unified memory: no
    Preferred profile: core (0x1)
    Max core profile version: 3.3
    Max compat profile version: 3.3
    Max GLES1 profile version: 1.1
    Max GLES[23] profile version: 3.1
OpenGL vendor string: Microsoft Corporation
OpenGL renderer string: D3D12 (NVIDIA GeForce RTX 3050 Ti Laptop GPU)
OpenGL core profile version string: 3.3 (Core Profile) Mesa 22.0.5
OpenGL core profile shading language version string: 3.30
OpenGL core profile context flags: (none)
OpenGL core profile profile mask: core profile

OpenGL version string: 3.3 (Compatibility Profile) Mesa 22.0.5
OpenGL shading language version string: 3.30
OpenGL context flags: (none)
OpenGL profile mask: compatibility profile

OpenGL ES profile version string: OpenGL ES 3.1 Mesa 22.0.5
OpenGL ES profile shading language version string: OpenGL ES GLSL ES 3.10
```

On newer version of MESA, the d3d12 backend will always [default](https://gitlab.freedesktop.org/mesa/mesa/-/merge_requests/17005) to the first enumerated integrated GPU if no user selection is specified. So on a laptop like the Surface Laptop Studio above the default GPU will be the Intel GPU and the user has to manually opt-in for the NVIDIA GPU if they so desire. This is done to avoid accidentally waking up the more powerful (and power hungry) GPU unless this is what the user wants as this has an impact on battery life.