# Creating Partitions and Formatting Filesystems Overwriting the Partition

&#x20;

**Creating partitions and formatting filesystems, commands(lsblk-fdisk-partprobe-mkfs), How to overwrite the partition table, if you create partition how to write data on this partition.**

What happens when you add a disk to your machine?

* To display the disks that are attached to your machine:
  * **If you add a disk to your machine, you will never see it if your machine is up and running.**
  * **When you boot your machine, the BIOS tries to detect hardware connected to your machine.**
  * **If you are on a physical machine and want to detect a disk, you will restart the machine.**
  * **If you are on a virtual machine and want to detect a disk, you will shut down the machine.**
* The BIOS configuration is stored in a file. This file is read only when you start the machine. The BIOS does not read this file again and again.
* In the case of a virtual machine, you can force your machine to reload its configuration without shutting down.

&#x20;

**Creating partitions and formatting filesystems (Lab)**

* To display the disks that are attached to your machine:
  * `fdisk -l`
* You have to figure out the partition scheme your disk uses, e.g., MBR (Master Boot Record).
  * The MBR name is also called `dos` (Disk Operating System) --> partition schema.\
    `Disk label type: dos`
*   The system gives each partition type a different system ID so it can know which partition is normal or otherwise.\
    **Example:**

    text

    ```
    Device Boot    Start    End    Blocks    Id    System
    /dev/sda1      *        2048   1026047  512000  83    Linux
    /dev/sda2             1026048  209715199 104344576 8e   Linux LVM
    ```
* Every partition has an ID. Not all partitions are the same; there are normal partitions like FAT, NTFS, and there are special partitions like those for LVM (you will know about this in the next lessons).
* To deal with the disk itself, not a partition inside the disk:
  * `fdisk /dev/sdb` ## Identify the disk you want to deal with.
*   The system leaves free space at the beginning, and you start after it.\
    Example prompts in fdisk:

    text

    ```
    First sector (2048-41943039, default 2048):
    Select (default p): p
    Partition number (1-4, default 1): 1
    First sector (2048-41943039, default 2048):
    Using default value 2048
    Last sector, +sectors or +size(K,M,G) (2048-41943039, default 41943039): +5G
    ```

    This means you are using the free space and creating a 5 GB partition.
* Before applying anything, you have to make sure everything is okay. But how? Before you write or save.

Creating partitions and formatting filesystems, commands(lsblk-fdisk-partprobe-mkfs), How to overwrite the partition table, if you create partition how to write data on this partition.

&#x20;

All changes are in memory. So you can change your mind.

* **Quick revision on MBR:**
  * MBR is divided into three parts and its size is 512 bytes:
    * Partition Table (PT): 64 bytes.
    * Boot Loader: 446 bytes.
    * Magic Number: 2 bytes (like a checksum, used for data recovery).
  * The kernel reads the MBR (the part called the partition table):
    * When you boot the machine, or
    * You attach a disk to your machine when it is running (hot-swappable disk).
  * If you add a new partition to the disk, the partition table is changed.
    * Does the kernel re-read the partition table? No.
    * You must inform the kernel that the partition table has been changed.
* To re-read the partition table or to scan if new partitions are added or not in the partition table:

Creating partitions and formatting filesystems, commands(lsblk-fdisk-partprobe-mkfs), How to overwrite the partition table, if you create partition how to write data on this partition.

&#x20;

`partprobe /dev/sdb`

This command forces a re-read of the partition table for the specific disk `/dev/sdb`. If you don't specify a disk, this command will scan all disks for changes in their partition tables.

* `mkfs` command: To create a filesystem on a partition, not on a disk.\
  `mkfs.ext4` or `mkfs.xfs` (Most people work with these two).\
  Example: `mkfs.ext4 /dev/sdb1`
* **Block size:** It is something important for you. Suppose you have large files on the partition; you need a specific block size. Therefore, increasing the block size makes operations faster and prevents the file from being split across many blocks.
  * It will reduce the number of inodes on the inode table.
  * Every inode points to only one block.
  * Note: The filesystem has a default block size, or you can customize it.
* **Inode table (Superblock):** Stores metadata about the files like permissions, size, owner. It is called the superblock. Multiple copies are stored in different parts of the partition. The number of copies depends on the partition size (like the index of a book; the bigger the book, the more indices it has).
  * If the inode table is removed, you can't access any file because you won't know which block the file is located in.

Creating partitions and formatting filesystems, commands(lsblk-fdisk-partprobe-mkfs), How to overwrite the partition table, if you create partition how to write data on this partition.

&#x20;

* How to delete a partition: `fdisk /dev/sdb`
  * You must save (write) and then run `partprobe` to reflect the modification in the partition table.
* The partition may seem deleted, but the partition table still shows it until you update it.
  * `fdisk -l /dev/sdb`
* If you want to destroy the partition table and return the disk to a blank state, you can corrupt the MBR.
* There are special files in Linux that write a lot of random data:
  * `cat /dev/random` – Writes continuous random data.
  * `cat /dev/urandom` – The same idea, writes continuous random data very fast.
  * Note: Writing to the terminal may corrupt it. To reset the terminal, use the command `reset`.
  * You can use these files to write random data to the MBR:\
    `cat /dev/urandom >> /dev/sdb`

Creating partitions and formatting filesystems, commands(lsblk-fdisk-partprobe-mkfs), How to overwrite the partition table, if you create partition how to write data on this partition.

&#x20;

But this method is not accurate. You want to overwrite only the first 512 bytes (the MBR) in a more controlled way.

* Use the zero special file instead of random and urandom.
  * `dd` (disk dump): `if` (input file), `of` (output file).\
    Example:\
    `dd if=/dev/zero of=/dev/sdb count=512 bs=1`
  * `bs=1` (block size = 1 byte), `count=512` -> 512 \* 1 = 512 bytes.
*   Example of using random:

    text

    ```
    [root@server ~]# dd if=/dev/random of=/dev/sdb count=512
    512+0 records in
    512+0 records out
    512 bytes (512 B) copied, 0.592599 s, 864 B/s
    [root@server ~]# dd if=/dev/random of=/dev/sdb count=512 bs=1M
    dd: warning: partial read (78 bytes); suggest iflag=fullblock
    0+0 records in
    0+0 records out
    0 bytes (0 B) copied, 0.590405 s, 0.0 B/s
    ```

    In the example, you wrote to the first 512 bytes. But if you want to overwrite more, you might also overwrite superblocks that will be created when you make a new filesystem.
* `mkfs.xfs -f /dev/sdb1` – The `-f` force option will write new superblocks for XFS, even if the disk has old superblocks from a previous filesystem (like ext4) installed on the disk partitions before. This can cause data loss because it will overwrite the old superblocks.
* `-i size` (inode size) option:\
  `[root@server ~]# mkfs.xfs -f -isize=512 /dev/sdb1`

**If you create a partition, how to write data on this partition?**

* `df -h` may show the formatted partition, but it might not be accessible like in Windows when you assign a drive letter to make it recognizable and you can interact with it.

Creating partitions and formatting filesystems, commands(lsblk-fdisk-partprobe-mkfs), How to overwrite the partition table, if you create partition how to write data on this partition.

&#x20;

* `mount /dev/sdb1 /media/` – I want to access or read it under `/media`.
* `cp -r /etc /media` – You wrote data to the partition when you mounted it and copied files.
* `ls /media` – The data exists on the partition. But if you unmount the partition, the data is not accessible (you won't see it). It's exactly like a flash drive; you copy files onto it, then eject it; the data is not accessible. When you mount the flash drive again, you can see the files.
* `dumpe2fs /dev/sdb1` – This command works with ext2, ext3, ext4. It gives you complete information about the partition.
* `e2fsck /dev/sdb1` – Filesystem check. It checks only the inode tables to see if they are stable or not. It does not check the entire disk.
  * Note: You cannot run `e2fsck` on a mounted filesystem. It's like performing an operation on a patient while they are walking.
  * First, unmount: `umount /dev/sdb1`. Then run the check.
  * `e2fsck -f /dev/sdb1` – The `-f` forces a check even if the filesystem seems clean. **Make a backup of the partition before using `-f`**.
* If there are problems, it will fix them. These problems can occur if, for example, the power goes out and there are issues with the data, and you run the check.
* **It is advisable to create a backup for the partition, even if you think it's not accessible.**
  * `dd if=/dev/sdb1 of=/sdb1-backup` – This will create a copy of the partition and put it in a file on your system.
