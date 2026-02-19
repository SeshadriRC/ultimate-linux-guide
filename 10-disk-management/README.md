# Disk and Storage Management in Linux

## Introduction to Disk and Storage Management
Managing disks and storage efficiently is crucial for system performance and stability. Linux provides various commands to monitor, partition, format, mount, and manage disk storage.

## Index of Commands Covered

### Viewing Disk Information
- `lsblk` – Display block devices
- `fdisk -l` – List disk partitions
- `blkid` – Show UUIDs of devices
- `df -h` – Check disk space usage
- `du -sh /path` – Show size of a directory

### Partition Management
- `fdisk /dev/sdX` – Create and manage partitions
- `parted /dev/sdX` – Alternative to `fdisk` for GPT disks
- `mkfs.ext4 /dev/sdX1` – Format a partition as ext4
- `mkfs.xfs /dev/sdX1` – Format a partition as XFS

### Mounting and Unmounting
- `mount /dev/sdX1 /mnt` – Mount a partition
- `umount /mnt` – Unmount a partition
- `mount -o remount,rw /mnt` – Remount a partition as read-write
- `mount -a` - Mount ALL filesystems mentioned in /etc/fstab that are not currently mounted.

### Logical Volume Management (LVM)
- `pvcreate /dev/sdX` – Create a physical volume
- `vgcreate vg_name /dev/sdX` – Create a volume group
- `lvcreate -L 10G -n lv_name vg_name` – Create a logical volume
- `mkfs.ext4 /dev/vg_name/lv_name` – Format an LVM partition
- `mount /dev/vg_name/lv_name /mnt` – Mount an LVM partition

### Swap Management
- `mkswap /dev/sdX` – Create a swap partition
- `swapon /dev/sdX` – Enable swap space
- `swapoff /dev/sdX` – Disable swap space

## Viewing Disk Information
### Using `lsblk`
List all block devices:
```bash
lsblk

NAME      MAJ:MIN RM SIZE RO TYPE MOUNTPOINTS
xvda      202:0    0   8G  0 disk
├─xvda1   202:1    0   8G  0 part /
├─xvda127 259:0    0   1M  0 part
└─xvda128 259:1    0  10M  0 part /boot/efi
```
### Using `fdisk`
View partition details:
```bash
fdisk -l

[root@ip-172-31-10-248 ~]# fdisk -l
Disk /dev/xvda: 8 GiB, 8589934592 bytes, 16777216 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: D320B4AF-A7CC-406A-95C6-698CB954EADE

Device       Start      End  Sectors Size Type
/dev/xvda1   24576 16777182 16752607   8G Linux filesystem
/dev/xvda127 22528    24575     2048   1M BIOS boot
/dev/xvda128  2048    22527    20480  10M EFI System

Partition table entries are not in disk order.


Disk /dev/xvdb: 5 GiB, 5368709120 bytes, 10485760 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```
### Using `df`
Check available disk space:
```bash
df -h

[root@ip-172-31-10-248 ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        4.0M     0  4.0M   0% /dev
tmpfs           481M     0  481M   0% /dev/shm
tmpfs           193M  444K  192M   1% /run
/dev/xvda1      8.0G  1.6G  6.4G  20% /
tmpfs           481M     0  481M   0% /tmp
/dev/xvda128     10M  1.3M  8.7M  13% /boot/efi
tmpfs            97M     0   97M   0% /run/user/1000
```
### Using `du`
Find the size of a directory:
```bash
du -sh /var/log
```

## Partition Management
### Creating a Partition with `fdisk`
```bash

fdisk /dev/xvdb
n                         # new partition
p                         # partition
1                         # default
enter
enter
p
w
mkfs.ext4 /dev/xvdb1
mkdir -p /mnt/demo-vol
mount /dev/xvdb /mnt/demo-vol/

# Practicals
Do you want to remove the signature? [Y]es/[N]o: Y

The signature will be removed by a write command.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2):
First sector (4196352-10485759, default 4196352):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-10485759, default 10485759): +2G

Created a new partition 2 of type 'Linux' and of size 2 GiB.

Command (m for help): n
Partition type
   p   primary (2 primary, 0 extended, 2 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (3,4, default 3):
First sector (8390656-10485759, default 8390656):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (8390656-10485759, default 10485759): +2G
Value out of range.
Last sector, +/-sectors or +/-size{K,M,G,T,P} (8390656-10485759, default 10485759): +1G
Value out of range.
Last sector, +/-sectors or +/-size{K,M,G,T,P} (8390656-10485759, default 10485759): +500M

Created a new partition 3 of type 'Linux' and of size 500 MiB.

Command (m for help): p
Disk /dev/xvdb: 5 GiB, 5368709120 bytes, 10485760 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x94f335ad

Device     Boot   Start     End Sectors  Size Id Type
/dev/xvdb1         2048 4196351 4194304    2G 83 Linux
/dev/xvdb2      4196352 8390655 4194304    2G 83 Linux
/dev/xvdb3      8390656 9414655 1024000  500M 83 Linux

Filesystem/RAID signature on partition 1 will be wiped.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
Follow the interactive prompts to create a partition.

### Deleting a partition with `fdisk`

```
fdisk /dev/xvdb
d
1                    # partition id
w                    # write changes
```

### Formatting a Partition
Format as ext4:
```bash
mkfs.ext4 /dev/sdX1
```
Format as XFS:
```bash
mkfs.xfs /dev/sdX1
```

## Mounting and Unmounting
### Mount a Partition
```bash
mount /dev/sdX1 /mnt
```
### Unmount a Partition
```bash
umount /mnt

umount /mnt/demo-vol
```
### Remount a Partition
```bash
mount -o remount,rw /mnt
```

## LVM Management
### Create a Physical Volume
```bash
pvcreate /dev/sdX

[root@ip-172-31-8-162 ~]# pvcreate /dev/xvdb
  Physical volume "/dev/xvdb" successfully created.
[root@ip-172-31-8-162 ~]# pvs
  PV         VG Fmt  Attr PSize PFree
  /dev/sdb      lvm2 ---  5.00g 5.00g
```
### Create a Volume Group
```bash
vgcreate vg_name /dev/sdX

[root@ip-172-31-8-162 ~]# vgcreate vg-vol1 /dev/xvdb
  Volume group "vg-vol1" successfully created

[root@ip-172-31-8-162 ~]# vgs
  VG      #PV #LV #SN Attr   VSize  VFree
  vg-vol1   1   0   0 wz--n- <5.00g <5.00g
```
### Create a Logical Volume

```bash
lvcreate -L 10G -n lv_name vg_name

[root@ip-172-31-8-162 ~]# lvcreate -L 4G -n lv_mylv vg-vol1

[root@ip-172-31-8-162 ~]# lvs
  LV      VG      Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv_mylv vg-vol1 -wi-a----- 4.00g
```
### Format and Mount the Logical Volume
```bash
mkfs.ext4 /dev/vg_name/lv_name
mount /dev/vg_name/lv_name /mnt

mkfs.ext4 /dev/vg-vol1/lv_mylv
mount /dev/vg-vol1/lv_mylv /sesha/demo

[root@ip-172-31-8-162 ~]# df -h /sesha/demo
Filesystem                    Size  Used Avail Use% Mounted on
/dev/mapper/vg--vol1-lv_mylv  3.9G   24K  3.7G   1% /sesha/demo
[root@ip-172-31-8-162 ~]# lsblk
NAME               MAJ:MIN RM SIZE RO TYPE MOUNTPOINTS
xvda               202:0    0   8G  0 disk
├─xvda1            202:1    0   8G  0 part /
├─xvda127          259:0    0   1M  0 part
└─xvda128          259:1    0  10M  0 part /boot/efi
xvdb               202:16   0   5G  0 disk
└─vg--vol1-lv_mylv 253:0    0   4G  0 lvm  /sesha/demo
```
## Now delete the PV which we created

### Delete Logical Volume (LV)

```bash
lvremove /dev/vg-name/lv-name

lvremove /dev/vg-vol1/lv_mylv
[root@ip-172-31-8-162 ~]# lvremove /dev/vg-vol1/lv_mylv
Do you really want to remove active logical volume vg-vol1/lv_mylv? [y/n]: y
  Logical volume "lv_mylv" successfully removed.

[root@ip-172-31-8-162 ~]# lvs             # it won't show anything
```

### Delete Volume Group (VG)

```bash
vgremove <vg-group-name>

[root@ip-172-31-8-162 ~]# vgremove vg-vol1
  Volume group "vg-vol1" successfully removed

[root@ip-172-31-8-162 ~]# vgs
[root@ip-172-31-8-162 ~]#
```

### Delete Physical volume

```bash
pvremove <device-name>

pvremove /dev/xvdb

[root@ip-172-31-8-162 ~]# pvremove /dev/xvdb
  Labels on physical volume "/dev/xvdb" successfully wiped.
[root@ip-172-31-8-162 ~]# pvs
[root@ip-172-31-8-162 ~]#

[root@ip-172-31-8-162 ~]# lsblk
NAME      MAJ:MIN RM SIZE RO TYPE MOUNTPOINTS
xvda      202:0    0   8G  0 disk
├─xvda1   202:1    0   8G  0 part /
├─xvda127 259:0    0   1M  0 part
└─xvda128 259:1    0  10M  0 part /boot/efi
xvdb      202:16   0   5G  0 disk
```

---
## Swap Management
### Create a Swap Partition
```bash
mkswap /dev/sdX
```
### Enable Swap
```bash
swapon /dev/sdX
```
### Disable Swap
```bash
swapoff /dev/sdX
```

## Additional Notes - When to Use fdisk, mount, or Both
### Check Available Disks
Before creating or mounting anything, always check what block devices exist:
```bash
lsblk
```
### Example output:
|NAME | MAJ:MIN| RM| SIZE |RO | TYPE| MOUNTPOINT|
|-----|---------|--|------|---|-----|-----------|
|sda   |  8:0   | 0 | 100G | 0 | disk|           |
|├─sda1|   8:1  | 0 |  96G | 0 | part| /         |
|└─sda2|   8:2  | 0 |  4G  | 0 | part| [SWAP]    |
|sdb   |   8:16 | 0 |  20G | 0 | disk|           |

`sda` → existing disk (already partitioned)

`sdb` → new disk, no partitions yet

### When to use `fdisk`
Use `fdisk` when:
- The disk is brand new and has no partitions
- You want to create `/dev/sdb1`, `/dev/sdb2`, etc.

Inside `fdisk`:
1. Press `n` → create a new partition
2. Press `w` → write changes

Then confirm:
```bash
lsblk
```
### When to Use `mount`
Use `mount` when:
The partition already exists and is formatted
You just want to make it accessible
```bash
sudo mkdir /mnt/mydisk
sudo mount /dev/sdb1 /mnt/mydisk
```
Now your disk is available at `/mnt/mydisk`.

### When to Use fdisk + mount (Full Setup)
Use `fdisk + mkfs + mount` when:
The disk is completely new
You need to partition → format → mount it
```bash
# 1. Check available disks
lsblk
# 2. Create partition
sudo fdisk /dev/sdb
# 3. Format the partition
sudo mkfs.ext4 /dev/sdb1
# 4. Mount it
sudo mkdir /data
sudo mount /dev/sdb1 /data
```

### To make changes permanent

- Add entry in /etc/fstab

```bash
[root@ip-172-31-10-248 demo]# blkid /dev/xvdb1
/dev/xvdb1: UUID="5d7c3a2a-1928-423c-9797-88bb08f45548" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="94f335ad-01"

[root@ip-172-31-10-248 demo]# vi /etc/fstab

[root@ip-172-31-10-248 demo]# mount -a

[root@ip-172-31-10-248 demo]# cat /etc/fstab
#
UUID=7813e2d4-9cdc-416a-a749-25de8a9f36d0     /           xfs    defaults,noatime  1   1
UUID=5d7c3a2a-1928-423c-9797-88bb08f45548    /sesha/demo  ext4   defaults,nofail   0   2
mount -a

```
### Quick Reference

| Use Case                     | Command(s)                  |
|-------------------------------|-----------------------------|
| View disks and partitions     | `lsblk`                     |
| Partition a new disk          | `fdisk`                     |
| Mount an existing partition   | `mount`                     |
| Full setup (new disk)         | `fdisk + mkfs + mount`      |


******************************************************************************************
## My-notes
******************************************************************************************

Good question — and your `lsblk` output is a **perfect real example** to explain this clearly.

Let’s break it down simply.

---

# 🔹 Difference between **partition (part)** and **LVM (lvm)**

## 1️⃣ Partition (`part`)

A **partition** is a **fixed slice of a disk**.

Example from your output:

```
xvda1   8G   part   /
```

Meaning:

* Disk: `xvda`
* Partition: `xvda1`
* Size: `8G`
* Mounted at: `/`

### Key properties:

* Fixed size
* Hard to resize
* Traditional disk management
* If full → resizing is **complex**

---

## 2️⃣ LVM (`lvm`) — Logical Volume Manager

LVM is a **flexible storage management layer** on top of disks.

Example from your output:

```
xvdb                 5G   disk
└─ec2--vol--1-lv_name 4G   lvm   /sesha/demo
```

Meaning:

* Disk: `xvdb`
* LVM Logical Volume: `ec2--vol--1-lv_name`
* Mounted at: `/sesha/demo`

### Key properties:

* Dynamic resizing (extend / shrink)
* Can combine **multiple disks**
* Can take **snapshots**
* Very flexible

---

# 🔹 How storage flows in LVM

```
Disk (/dev/xvdb)
    ↓
Physical Volume (PV)
    ↓
Volume Group (VG)
    ↓
Logical Volume (LV)
    ↓
Filesystem + Mount
```

Example:

```
/dev/xvdb → PV → VG → LV → /sesha/demo
```

---

# 🔹 Key Differences — Simple Table

| Feature                | Partition (part) | LVM (lvm)   |
| ---------------------- | ---------------- | ----------- |
| Flexibility            | ❌ Low            | ✅ Very High |
| Resize                 | ❌ Hard           | ✅ Easy      |
| Combine multiple disks | ❌ No             | ✅ Yes       |
| Snapshots              | ❌ No             | ✅ Yes       |
| Enterprise usage       | ❌ Rare           | ✅ Standard  |
| Performance            | Same             | Same        |

---

# 🔹 Your System Explained

### Root filesystem (Traditional partition):

```
xvda1  →  /    (8G part)
```

### Extra disk using LVM:

```
xvdb → LVM → /sesha/demo
```

This is **best practice**:

* OS disk → normal partition
* Data disk → LVM

---

# 🔹 When should you use LVM?

Use **LVM** when:

* Database storage
* Application data
* Log partitions
* Growing filesystems
* Production servers

Avoid LVM when:

* Boot partitions
* Very small systems
* Embedded systems

---

# 🔹 Real-world Example (Your Case)

If `/sesha/demo` becomes full:

With **partition** → Painful resize
With **LVM** → Just 2 commands:

```bash
lvextend -L +2G /dev/ec2-vol-1/lv_name
xfs_growfs /sesha/demo
```

🔥 No reboot, no downtime.

---

If you want, I can give you **complete hands-on LVM lab** with:

* create → extend → snapshot → restore → delete

Perfect for interviews + real admin work.
