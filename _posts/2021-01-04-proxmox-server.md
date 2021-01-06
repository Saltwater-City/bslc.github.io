---
layout: post
title: Guide to Using Proxmox, Containers, and GPU Pass Through for Machine Learning
tags: proxmox, GPU, machine-learning, container, pyenv, virtualenv, home-lab
---

Setting up a home lab with GPU support can be helpful in learning machine learning and other technologies. While we could use virtual machines on Proxmox, we will focus on using containers with GPU pass through in this guide. Aside from making a few updates, [this post](https://medium.com/@MARatsimbazafy/journey-to-deep-learning-nvidia-gpu-passthrough-to-lxc-container-97d0bc474957) has most of the details necessary to setup the home lab properly. I am going to detail my experience in setting up a lab with Nvidia 1070 Ti and Core i7 setup. 

## Installing Proxmox

To install Proxomox, you will need to create a USB key with the installation software. I download Proxomox 6.3 and use a Mac to create the installation media.

1. Download Proxmox 
    
    You can download the latest version of Proxmox directly [at this page](https://www.proxmox.com/en/downloads/category/iso-images-pve).
2. Creating the USB key
    
    I created the key on macOS Cantalina. For other OSes, you can find the instructions at the [Proxmox wiki](https://pve.proxmox.com/wiki/Prepare_Installation_Media).

    ``` bash
    hdiutil convert -format UDRW -o proxmox-ve_*.dmg proxmox-ve_*.iso # convert the proxmox image into proper format
    ```
    After converting the image file, plug the USB key into the Mac and looking for the disk.
    ``` bash
    # look for the external disk
    diskutil list # list all the disk attached to the computer
    ```
    From the list of disks, there should be an external disk. We need to unmount it prior to writing the installation media.
    ``` bash
    diskutil unmountDisk /dev/diskX # replace X with the number corresponding to the external disk
    ```
    Now, you can create the install media using [dd](https://en.wikipedia.org/wiki/Dd_(Unix))
    ``` bash
    sudo dd if=proxmox-ve_*.dmg of=/dev/rdiskX bs=1m
    ```
## Installing Proxmox
1. Installing Proxmox
   
   Aside from a making a couple of choices on the file system and the address of the home lab, installation of Proxmox is straight forward.

2. Logging into Proxomox's Web Interface
   
   At this point, you can unplug the home lab box's monitor and keyboard. The rest of the guide can be done via a terminal or Proxmox's web interface.
    ```
    https://address.you.chose:8006
    ```
    Port 8006 is the default port number if you did not change it during the installation process. On visiting the web interface from a browser, the server would prompt for user name and password. The default user name is root and the password would have been chosen during the install. 
    If the page fails to load, you may have forgotten the `s` in `https`. Because the `s` is needed, your browser may give you are warning about the webpage being insecure. You may safely ignore it. 

## Configuring Proxomox for GPU Pass Through

Making GPU pass through work on Proxmox and containers is essentially a two step process:
    
    1. configure the drivers on the server, and
    2. configure the containers. 

1. Configure the Nvidia Drivers on the Server

    Either through Promox web interface or login to the server directly via SSH, we need to have command line access to the server. Since Proxmox is based on Debian, we follow the steps outlined in the [Debian wiki](https://wiki.debian.org/NvidiaGraphicsDrivers) to install the drivers on Proxmox. You can find references to package repositories in the [Proxmox wiki](https://pve.proxmox.com/wiki/Package_Repositories).

    We need to add the following lines to `/etc/apt/sources.list`:
    ``` bash
    # security updates
    deb http://security.debian.org buster/updates main contrib
    
    # PVE pve-no-subscription repository provided by proxmox.com,
    # NOT recommended for production use
    deb http://download.proxmox.com/debian buster pve-no-subscription
    
    # buster-backports
    deb http://deb.debian.org/debian buster-backports main contrib non-free
    ```
    I used Proxmox 6.3 in writing this guide and it is baased on Debian 10. If you are using a different version of Proxmox, it might be based off a different Debian version. In that case, you need to change the word `buster` to the corresponding version. 

    Update all the packages to the latest versions and reboot
    ``` bash
    apt-get update
    apt-get dist-upgrade
    shutdown -r now
    ```

    We need to verify the kernel version and the corresponding headers with 
    ``` bash
    uname -r
    apt-cache search pve-header
    ```

    For me, I got this output
    ``` bash
    root@machinename:~# uname -r
    5.4.78-2-pve
    root@machinename:~# apt-cache search pve-header
    pve-headers-5.0.12-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.0.15-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.0.18-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.0.21-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.0.21-2-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.0.21-3-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.0.21-4-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.0.21-5-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.0.8-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.0.8-2-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.0 - Latest Proxmox VE Kernel Headers
    pve-headers-5.3.1-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.3.10-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.3.13-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.3.13-2-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.3.13-3-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.3.18-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.3.18-2-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.3.18-3-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.3.7-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.3 - Latest Proxmox VE Kernel Headers
    pve-headers-5.4.22-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.24-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.27-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.30-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.34-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.41-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.44-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.44-2-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.55-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.60-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.65-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.73-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.78-1-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4.78-2-pve - The Proxmox PVE Kernel Headers
    pve-headers-5.4 - Latest Proxmox VE Kernel Headers
    pve-headers - Default Proxmox VE Kernel Headers
    ```
    
    We can install the proper version with
    ``` bash
    apt-get install pve-headers-5.4.78-2-pve
    ```

    and install Nvidia drivers with
    ```bash
    apt-get install -t buster-backports nvidia-driver
    ```

    Like I mentioned before, you may need to change the version number if you are installing with a different version of Proxmox.

    You can install some tools to go along with the driver:
    ``` bash
    apt-get install i7z nvidia-smi htop iotop
    ```

    If you check `/dev` now, there should be some Nvidia related files:
    ```bash
    root@machinename:~# ls -alh /dev/nvid*
    crw-rw-rw- 1 root root 195, 254 Dec 27 01:16 /dev/nvidia-modeset
    crw-rw-rw- 1 root root 235,   0 Dec 27 01:16 /dev/nvidia-uvm
    crw-rw-rw- 1 root root 235,   1 Dec 27 01:16 /dev/nvidia-uvm-tools
    crw-rw-rw- 1 root root 195,   0 Dec 27 01:16 /dev/nvidia0
    crw-rw-rw- 1 root root 195, 255 Dec 27 01:16 /dev/nvidiactl
    ```

    To ensure that these drivers are loaded at boot time, you need to edit `/etc/modules-load.d/modules.conf` with your favourite editor and add
    ```bash
    # /etc/modules: kernel modules to load at boot time.
    #
    # This file contains the names of kernel modules that should be loaded
    # at boot time, one per line. Lines beginning with "#" are ignored.

    nvidia
    nvidia_uvm
    ```
    
    Because `nvidia` and `nvidia_uvm` are not automatically created until X-server or nvidia-smi is called, we need to add the following lines to `/etc/udev/rules.d/70-nvidia.rules`:
    
    ```bash
    # /etc/udev/rules.d/70-nvidia.rules
    # Create /nvidia0, /dev/nvidia1 … and /nvidiactl when nvidia module is loaded
        KERNEL==”nvidia”, RUN+=”/bin/bash -c ‘/usr/bin/nvidia-smi -L && /bin/chmod 666 /dev/nvidia*’”
    # Create the CUDA node when nvidia_uvm CUDA module is loaded
        KERNEL==”nvidia_uvm”, RUN+=”/bin/bash -c ‘/usr/bin/nvidia-modprobe -c0 -u && /bin/chmod 0666 /dev/nvidia-uvm*’”
    ```

    Now, reboot the server with `shutdown -r now` and check if everything worked with `nvidia-smi` in a new command line.

2. Configure the Containers:

    Find the container ID in Proxmox's web interface and then edit the corresponding file at `/etc/pve/lxc/`. I am using an Ubuntu container with ID 100 so here's my config file
    ```bash
    #Ubuntu 20.04 with GPU passthrough
    arch: amd64
    cores: 10
    hostname: CT100
    memory: 16384
    net0: name=eth0,bridge=vmbr0,hwaddr=AA:98:43:03:D4:41,ip=dhcp,ip6=dhcp,type=veth
    ostype: ubuntu
    rootfs: local-zfs:basevol-100-disk-1,size=30G
    swap: 16384
    template: 1
    unprivileged: 1

    # GPU passthrough configs
    lxc.cgroup.devices.allow: c 195:* rwm
    lxc.cgroup.devices.allow: c 243:* rwm
    lxc.mount.entry: /dev/nvidia0 dev/nvidia0 none bind,optional,create=file
    lxc.mount.entry: /dev/nvidiactl dev/nvidiactl none bind,optional,create=file
    lxc.mount.entry: /dev/nvidia-uvm dev/nvidia-uvm none bind,optional,create=file
    lxc.mount.entry: /dev/nvidia-modeset dev/nvidia-modeset none bind,optional,create=file
    lxc.mount.entry: /dev/nvidia-uvm-tools dev/nvidia-uvm-tools none bind,optional,create=file
    ```

    Reboot the container for the settings to take effect. After the reboot check to see if the configurations worked with the following commands and outputs:
    
    ```bash
    # nvidia-smi
    Tue Jan  5 02:57:07 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 450.80.02    Driver Version: 450.80.02    CUDA Version: 11.0     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  GeForce GTX 107...  Off  | 00000000:01:00.0  On |                  N/A |
    |  0%   45C    P8     8W / 180W |      1MiB /  8116MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+

    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    ```
    
    and
    
    ```bash
    # ls /dev/nvidia* -l
    -rw-r — r — 1 root root 0 16.01.2017 20:11 /dev/nvidia-modeset
    crw-rw-rw- 1 nobody nobody 243, 0 16.01.2017 20:05 /dev/nvidia-uvm
    -rw-r — r — 1 root root 0 16.01.2017 20:11 /dev/nvidia-uvm-tools
    crw-rw-rw- 1 nobody nobody 195, 0 16.01.2017 20:05 /dev/nvidia0
    crw-rw-rw- 1 nobody nobody 195, 255 16.01.2017 20:05 /dev/nvidiactl
    ```

### Creating Snapshots and Templates

After all this hard work, you should save your progress on the container by creating a snapshot. If you chose ZFS as the base file system, this would be an option in the web interface under the specific container's menu. 

You could also make the new GPU enabled container a template as a basis for other containers. The option of making that cointainer a template can be found via right-clicking on the name of the container. 
    

### Final Thoughts

That's it. You are done. You can now move to more important work of using the home lab for learning or actual work. Good luck and let me know the cool projects you do with the GPU enabled Proxmox home lab.
