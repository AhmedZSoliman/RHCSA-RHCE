# LVM: Concepts, Labs, Integration with RAID, Extend & Shrink, Stripping, and Snapshots

## &#x20;

### LVM Concept

* **LVM**: Stands for Logical Volume Management.
* **Logical**: Forget any physical limitations.
* **Example**: You have a disk and its size is 500 GB (This is a physical limitation). Can you write 600 GB on a 500 GB disk? No.
* **The solution is LVM**: Add a new disk to the 500 GB disk logically.
* **Therefore, the space appears as their total sum**.
* **Its idea is that you have more than one partition or disk**.
* **LVM cannot deal directly with any physical disk**.
* **It deals with logical volumes**.
* **When it combines disks, it does so logically, not physically**.

***

***

* **Volume**: The logical thing, like a disk, has a name.
* **So, convert each physical disk to a logical volume**: Combine disks together (logical volumes) to create a...
* **Volume Group: Logical Volumes**: It is a group of...
* **Logical Disk: Logical Volumes**: It is a disk formed from a group.
* **Logical Disk: Logical Disk**: After you combine those spaces together, you can divide them logically into parts.
* **LVM**: You can have a partition with 50 GB and another partition with 100 GB. You might have a partition with a total space of 500 GB, and the remaining spaces don't exceed the total.
* **Why? Because when you make the Volume Group, for example, the first disk is divided into equal parts, the same for the second and third disk. Each part is called a Physical Extent**.
* The total space taken from the partitions or disks is the **Volume Group**.
* It gives you a total space of, for example, 13 GB. Can I add 13 GB to this Volume Group?
* They can be independent disks or a single logical disk.
* You can make from these 13 GB a 7 GB disk, a 5 GB disk, and a 1 GB disk.
* And when writing, it writes on the first **Physical Extent**, then the next, and so on.
* **Each Logical Volume has a number of Physical Extents**.
* **Each Physical Extent** has a size.
* **All Physical Extents have the same size**.
* **Default Extent size is 4 MB**.
* **For example**: If you want to make a Logical Volume of size 7 GB, how many extents do you want?\
  The solution: 1 GB = 1024 MB / 4 MB = 256 extents → 7 GB = 256 \* 7 = 1792 extents.

***

&#x20;

***

* For example, if you have a Logical Volume of size 512 MB, how many extents do you want?\
  512 MB / 4 MB = 128 extents? No, there is no such thing as a quarter extent; you take the whole number.\
  It will be 128 extents.

**In LVM**:

* Convert physical partitions to Physical Volumes.
* The conversion process is from the **physical partition**.
* Combining them gives you the **Volume Group**.
* Create a **Logical Volume** from the Volume Group.
* Create a **File System** for the Logical Volume before mounting.
* When you mount, you will mount the **Logical Volume**.
* When you deal with a Logical Volume, forget about the **partitions**.
* The file system is on the partition itself; no one should invent and create on it.

**LVM Lab**\
The disks are empty; they have nothing, no superblock, no MBR.

\[root@server \~]# dd if=/dev/zero of=/dev/sdb bs=1M count=1

You can make LVM on the entire disk or a partition on the disk.

**The first step in LVM**:

* `pvcreate /dev/partition_or_disk`: Convert physical partition or disk to Physical Volume.\
  Example:\
  \[root@server \~]# pvcreate /dev/sdb2 /dev/sdc

***

&#x20;

***

* **`pvs`**: Shows disks or partitions converted to Physical Volume.
* **`pvdisplay`**: Gives more details.

**The second step in LVM**:

* **`vgcreate group_name /dev/partition_or_disk /dev/partition_or_disk`**: Creates a Volume Group with the specified physical volumes as members.\
  Example:\
  \[root@server \~]# vgcreate data /dev/sdb1 /dev/sdc
* **`vgs`**: Scans Volume Groups.
* **`vgdisplay`**: Gives more details.

**The third step in LVM**:

* **`lvcreate --size --name logical_volume_name volume_group_name`**: Creates a Logical Volume.\
  Example: Create a Logical Volume of size 22 GB named `oracle` from Volume Group `data`.\
  \[root@server \~]# lvcreate --size 22G --name oracle data
* **`vgs`**: Scans Volume Groups.
* **`lvs`**: Scans Logical Volumes.
* **`lvdisplay`**: Gives more details.
* When you create a Logical Volume, the Volume Group `data` is displayed with a logical volume pool directory `/dev/data`. Inside this directory is a symlink.

Example:\
\[root@server \~]# ls /dev/data/\
oracle\
\[root@server \~]# ls -l /dev/data/oracle\
lrwxrwxrwx. 1 root root 7 Dec 26 20:41 /dev/data/oracle -> ../dm-3

**The fourth step in LVM**:

* **Create a file system on the Logical Volume**.

***

&#x20;

***

* `mkfs.ext4 /dev/volume_group/logical_volume`

Example:\
\[root@server \~]# mkfs.ext4 /dev/data/oracle

* Perform the mounting process.

Example:\
\[root@server \~]# mount /dev/data/oracle /oracle/

* `df -h`
* To make the mount permanent so it mounts automatically on boot, add it to `/etc/fstab`.
* LVM has configuration files under `/etc/lvm/`.
* There is also a backup folder for each Volume Group in `/etc/lvm/backup/`. This file contains all the changes you make on the Volume Group, whether you added a Logical Volume, deleted a Volume, or extended the space of one, etc. This file is very useful because it keeps a record of everything you have done.

Example:\
description = "Created _after_ executing 'lvcreate --size 22G --name oracle data'"

***

&#x20;

***

**LVM with RAID**\
LVM, as long as you have storage, does not resize from the end because it is a fixed size? No.\
RAID gives you a fixed size.\
LVM gives you a flexible size.

***

**The problem of LVM**: If one disk in the Volume Group fails, can you recover the data?

**The answer to the question**: RAID (bottom) under LVM (top).

* Because RAID gives me redundancy only, and LVM gives me flexibility to extend and shrink.
* If a disk in a RAID array fails for any reason, I will replace it, and you will add a new disk (like hitting anything). The data on it will be moved from here to the new disk you put in, but in the picture, it doesn't affect LVM.
* The method is not straightforward. If one disk in the LVM picture fails, the data still actually exists, but you will remove the Volume Group and add a new one. However, the data still exists because here in the example we do mirroring through RAID.
* Create a RAID device (make a RAID array) and add two devices (disks) to it.
* Display the RAID array status.

\[root@server \~]# cat /proc/mdstat

***

* Create a RAID device (make a RAID array) and add another two devices (disks) to it.

\[root@server \~]# mdadm --create /dev/md1 --level=1 --raid-devices=2 /dev/sd\[ab]

Note: The name for RAID arrays is md0, md1, etc. We added two disks in the first RAID (md0) and added a disk in the second RAID (md1). When it comes, it writes on the disk's superblock and writes RAID array info on it. The same idea: LVM does this by writing LVM metadata on the superblock. If you work them together, they will be on the same superblock. If you finish working on them and want to remove them, do zeroing to the superblock.

* Create Physical Volume.

\[root@server \~]# pvcreate /dev/md\[01]\
\[root@server \~]# pvs

* Create Volume Group.

\[root@server \~]# vgcreate data /dev/md\[01]\
\[root@server \~]# vgs

* Create Logical Volume.

\[root@server \~]# lvcreate --size=30G --name oracle data

* Make a file system on the Logical Volume.

\[root@server \~]# mkfs.ext4 /dev/data/oracle

Be careful: When you set up LVM, it doesn't take space from RAM.

***

**If a disk fails while you are working with RAID and LVM together, if you replace the disk, forget the disk itself, forget the RAID devices themselves. Every interaction you have is with the Logical Volume**.

* Perform the mounting process.
* Metadata is written when you do `pvcreate`, and `pvremove` allows clearing LVM metadata from the superblock. RAID has an option called `--zero-superblock`. We remove it from RAID, so if there is a problem or a disk fails and it was a simulation, we will do it... The world will find nothing is working. Put it like that; copy it like that.

**LVM Extend**\
LVM Extend: Increases the space of an existing Logical Volume.

Example: I have disks, and I convert them to Physical Volumes, make a Volume Group, and after that, I make a Logical Volume.

Example: The Volume Group is 40 GB, and I used 30 GB. I want to add 35 GB.

***

* **`lvextend`** is used to add size.
* If you extended the size to 30 GB in LVM, did you actually add to it? No, you changed it. But did you update the inode table for the file system? No, you must update it.
* Without re-reading the inode table: It needs to see the new blocks that are added.
* **`resize2fs`**: Updates the inode table and resizes the filesystem to the size you want.
* **After `lvextend`, you have to do `resize2fs`**.
* **The process of extending works for any filesystem, and you don't need to unmount this filesystem**.
* **The filesystem sees that new space has been added**.
* **It checks the inode tables to see if new blocks have been added or not**.

**LVM Shrink**

* **How to shrink LVM**?

***

* Go to the filesystem and tell it: I will reduce the number of your blocks.
* **This means reducing the disk space; you tell the filesystem: "Your space will decrease." The inode table reduces the number of blocks it points to**.
* **Before anything, checking the filesystem is a mandatory process. You must do it to ensure there are no problems in the inode table**.
* Example: Imagine a filesystem with a size of 40 GB, and the files on the disk occupy 30 GB. If you want to reduce it to 20 GB, where will the extra 10 GB go? To the trash.
* The first thing you will do is `resize2fs`.
* **Before reducing or shrinking the filesystem, you have to unmount the filesystem**. You must ensure that after reducing the space, it is enough to store the files.
* The second thing you will do is `lvreduce`.

Example: Shrinking

1. Unmount.\
   \[root@server \~]# df -h\
   Filesystem Size Used Avail Use% Mounted on\
   /dev/mapper/data-oracle 35G 473M 33G 2% /data\
   \[root@server \~]# umount /data/
2. Check the filesystem.\
   \[root@server \~]# e2fsck -f /dev/data/oracle
3. Resize the filesystem.\
   \[root@server \~]# resize2fs /dev/data/oracle 30G
4. `lvreduce`.\
   \[root@server \~]# lvs\
   LV VG Attr LSize Pool Origin Data% Meta% Move Log Cpy%Sync Convert\
   oracle data -wi-a----- 35.00g

***

**Stripping LVM**

* **How does LVM write data**?
* It starts with disks or partitions. You must convert each disk to a Physical Volume, combine them in a Volume Group (`vg`), then start cutting the space you want from the `vg`.
* When writing data, it divides each disk or partition into a number of **stripes**, each a quarter GB in size. It writes on the first disk or partition, then finishes and moves to the second disk, and so on. This is **Linear LVM**.
* **By default, LVM works linearly**.\
  Example: Assume you have four disks, each divided into stripes. When writing data, for example, all stripes on the first disk are written, then it goes to the second disk, and so on. So if one of the disks has a problem, take it out, but in reality, the data is on the first disk, then goes to the second. LVM makes them appear as one thing.
* **Stripping LVM**: Writes a bit on the first disk or partition, then goes to the second disk or partition and writes a bit. This is the striping method.
* Example: But here, you distribute them across the disks. Write a bit on the first, then a bit on the second, and so on. This is much better in terms of speed.
* **I/O High Performance**.

**LAB**

* Create a Physical Volume for each disk we have. In this example, 4 disks.\
  \[root@server \~]# pvcreate /dev/sd\[bcde]\
  \[root@server \~]# pvs
* Create a Volume Group.\
  \[root@server \~]# vgcreate data /dev/sd\[bcde]\
  \[root@server \~]# vgs\
  VG #PV #LV #SN Attr VSize VFree\
  data 4 0 0 wz--n- 79.98g 79.98g

It will distribute the data across the four disks.\
\[root@server \~]# lvcreate --size 40G --name oracle -i 4 data\
\[root@server \~]# lvs\
LV VG Attr LSize Pool Origin Data% Meta% Move Log Cpy%Sync Convert\
oracle data -wi-a----- 40.00g

\[root@server \~]# lvdisplay -m /dev/data/oracle\
\[root@server \~]# dmsetup deps /dev/data/oracle\
dependencies : (8, 64) (8, 45) (8, 32) (8, 16)

**LVM Snapshot**\
(This is like a picture of the thing you have).

***

* **You have existing data, and you want to write on it, like updates or modifications. If a problem occurs, what do you do?**
* **Therefore, a snapshot is an exact copy of the data, so if a problem occurs, you can revert to the original**.
* **Meaning: Delete the data and restore the data from the snapshot? No, merge: meaning keep the data as is, and the snapshot becomes part of it**.

**LAB**

* I want to write some data.\
  \[root@server \~]# mkfs.ext4 /dev/data/oracle\
  \[root@server \~]# mount /dev/data/oracle /data/\
  \[root@server \~]# cp -r /usr/ /data/\
  \[root@server \~]# df -h
* Example:\
  The written data is 5 GB. The snapshot size is 500 MB? The logical volume size should be anything except less than 5 GB; preferably larger. Because the data size might increase. So, if you take a snapshot and its size is fixed at 5 GB, but the actual data size increased beyond 5 GB, then you are in trouble because the snapshot size is fixed.
* It is preferred that the snapshot size be larger than the logical volume you are taking a snapshot of.
*   Create a snapshot point at a specific logical volume.

    \[root@server \~]# lvcreate --size 2G --name oracle-snap --snapshot /dev/data/oracle\
    Logical volume "oracle-snap" created.

    `s` means snapshot.

    \[root@server \~]# lvs\
    LV VG Attr LSize Pool Origin Data% Meta% Move Log Cpy%Sync Convert\
    oracle data owi-aos--- 40.00g\
    oracle-snap data swi-a-s--- 2.00g oracle 0.00
*   List:

    \[root@server \~]# lvdisplay /dev/data/oracle-snap\
    Logical volume ...\
    LV Path /dev/data/oracle-snap\
    LV Name oracle-snap\
    VG Name data\
    LV UUID z8dJUk-pjUK-RWQa-F4af-cky8-CWtU-d4fI6D\
    LV Write Access read/write\
    LV Creation host, time [server.example.com](https://server.example.com/), 2017-01-09 20:38:09 +0300\
    LV snapshot status active destination for oracle
*   When you revert the snapshot:

    \[root@server \~]# umount /data/\
    \[root@server \~]# lvconvert --merge /dev/data/oracle-snap\
    Merging of volume oracle-snap started.\
    oracle: Merged: 99.9%\
    \[root@server \~]# mount /dev/data/oracle /data/

***

Note: The snapshot uses something called **COW (Copy on Write)**. So, the snapshot process does not write on the original copy; it stops writing on it. You can say it makes it read-only.

So, from all the above, focus here: The snapshot is not the size of the data you took a picture of. The snapshot stops writing on the data that was written before. But the snapshot determines how much data you will write. So, the snapshot is the size of the data that will be written on the data that was written before.

Meaning, in addition to the original copy. For example, if the original data size is 5 GB, and the snapshot is 2 GB, then after you finish writing and the snapshot consumes those 2 GB, the total data you have will be 7 GB.

Example: If you have data of 3 GB and you make a snapshot of 6 GB, then the total size will be 9 GB. And here there is also a trick: the new data includes the data that will be deleted.

***

Meaning, does the new data include the data that will be deleted? Simply, if you have data of 3 GB and you make a snapshot of 5 GB, and then you delete 1 GB from those 3 GB, the snapshot will have that 1 GB that was deleted added to it. So, the available snapshot space for writing becomes 4 GB. Now, suppose you write data on the snapshot more than the snapshot size, then naturally the extra data will be lost, like a cup of water you spill.

So, the conclusion is that any addition or deletion of data from the original data, the percentage of the snapshot will increase, and it will still keep the original data because that is the idea of the snapshot in general.

***

So, the question: Should the snapshot size be large? This is a very tricky question. Because here you don't know how much data you will write. So you must know how much data you will write.

You use the snapshot in specific cases, not just because. You use it, for example, in the case of updates for the entire distribution, or in the case of backup, and so on.

Now, suppose you want to increase the space of this snapshot. You can do it automatically or manually.

In the case of the manual method via the command:

**lvextend --size=+5G /dev/data/oracle-snap**

And similarly for the automatic method, which is that you modify the LVM configuration file via:

**vim /etc/lvm/lvm.conf**

And modify this line:

**snapshot\_autoextend\_threshold = 100**

***

This line means it will do an extend once the snapshot space reaches 100%. Of course, you can't wait until the space reaches 100%. You can change it to 80, for example.

snapshot\_autoextend\_threshold = 80

And this line means: We will increase the snapshot space by a certain percentage.

snapshot\_autoextend\_percent = 20

A 20% rate is good, not bad.\
And by default, it does not do any extend; 100 means it will not do any extend.

<br>
