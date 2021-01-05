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

1. Configure the Nvidai Drivers on Server

    Either use the Promox web interface or login to the server directly via SSH, we need to have a p


