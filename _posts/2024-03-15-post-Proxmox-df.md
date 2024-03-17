---
title: "Digital forensics on Kali hosted on Proxmox"
date: 2023-03-15T15:34:30-04:00
categories:
  - Blog
tags:
  - Homelab
  - Forensics
  - Kali
  - Proxmox
---


## Why?

####  I was waiting on harddrives to come in for my new Proxmox host and was able to get a harddrive from an estate sale and found another one. To get my reps in for Digital forensics I decided to see what I could find on the drive using sluth kit.

## Prerequisites

- Proxmox
- Image of Kali
- Drive to be forensicified 
- Drive bigger then the drive to be forensicified by a decent amount


## Getting Kali setup

## Passing through drive that will be investigated

#### Source [Passthrough Physical Disk to Virtual Machine Proxmox](https://pve.proxmox.com/wiki/Passthrough_Physical_Disk_to_Virtual_Machine_(VM))

#### First open up a terminal on he host and install lshw

``` 
apt install lshw
```

#### List all drives on the host
``` 
lshw -class disk -class storage
```

#### This will give the results such as this

```
*-disk
                description: ATA Disk
                product: ST3000DM001-1CH1
                vendor: Seagate
                physical id: 0.0.0
                bus info: scsi@3:0.0.0
                logical name: /dev/sda
                version: CC27
                serial: Z1F41BLC
                size: 2794GiB (3TB)
                configuration: ansiversion=5 sectorsize=4096
```

#### Run this command to get more information on the disk

``` 
ls -l /dev/disk/by-id | grep Z1F41BLC
ls -l /dev/disk/by-id | grep {serial number from your disk} 
```

#### You'll then get an output along the lines of 

``` 
lrwxrwxrwx 1 root root 9 Jan 21 10:10 /dev/disk/by-id/ata-ST3000DM001-1CH166_Z1F41BLC -> ../../sda

lrwxrwxrwx 1 root root 9 Jan 21 10:10 /dev/disk/by-id/ata-{your-drive's info} -> ../../sda
```

#### Adding drive

```
qm set 592 -scsi2 /dev/disk/by-id/ata-ST3000DM001-1CH166_Z1F41BLC
update VM 592: -scsi2 /dev/disk/by-id/ata-ST3000DM001-1CH166_Z1F41BLC

qm set {VMID} -scsi2 /dev/disk/by-id/ata-{your drive's info}
update VM {VMID}: -scsi2 /dev/disk/by-id/ata-{your drive's info}
```

#### Then stop and restart the VM to make sure it arrives