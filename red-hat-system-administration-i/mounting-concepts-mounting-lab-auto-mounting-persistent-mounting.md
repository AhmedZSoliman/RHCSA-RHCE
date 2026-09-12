# Mounting Concepts, Mounting Lab, Auto-Mounting, Persistent Mounting

### **Mounting concept**

* **`/` (root file system):** Parent of itself.
* **For Example:** The first partition I consider as the root file system.

text

```
| 5G | 5G | 10G |
| 1   | 2   | 3   |
```

***

#### **Mount concept:**

* It means, for example, you want the mount point named `/data` to be associated with a partition.
* Or let the second partition be accessible through the `/data` location or mount point.
* **Mount:** The partition becomes accessible from a specific directory (mount point).
* **Unmount:** Not accessible.
* When you shut down the machine, the Root file system will unmount automatically.
* When you boot the machine, where is your root file system? (e.g., in partition one).
* For example, the second partition: I want to mount this partition.
* So, the slash (`/`) is the root file system, and anything you want to mount will be under the slash. Don't mount it directly under slash itself. Make mounting under any directory under the slash.
* When you mount a partition to a specific mount point (accessible from a specific directory), you are essentially linking that partition to that directory.
* **For example:** Root file system points at partition number one.

***

&#x20;

&#x20;

* I install OS in a partition, its size is 5 GB (Partition 1).
* I mount partition 2 under `/media` and I mount partition 3 under `/media`.
  * The last mount will be the one accessible. The previous mount under `/media` will be overridden (unmounted). After unmounting, if you mount something else under `/media`, the directory will no longer point to the previous partition; it will be part of the newly mounted partition.

**The syntax of mount:**

* `mount -t type(filesystem) dev mount_point`
  * **Example:** `mount -t ext4 /dev/sda1 /media`
  * You don't have to specify the type; it will be detected automatically if you don't specify.

**The syntax of umount:**

* `umount dev` or `umount mount_point`
  * **Example:** `umount /dev/sdb` or `umount /media`

***

**Mounting Lab**

* Remember, before you format any partition, make sure it is not usable (mounted).
* Check through these commands: `lsblk` or `df -h` (`-h` for human-readable format).
* `fdisk /dev/sda` – Create an extended partition if you want to make logical partitions.
  * Every partition type has a different ID number.
* `mkfs.ext4 /dev/sdb1` – Create a filesystem.
  * Don't format the extended partition; format the logical partition inside the extended.
* `mount /dev/sdb1 /media`
*   Use these commands:

    text

    ```
    root@server ~]# cp -r /etc/ /media/
    root@server ~]# cp -r /usr/ /media/
    root@server ~]# df -h
    ```
* Write a test file:\
  `root@server ~]# dd if=/dev/zero of=/media/bigfile bs=1M count=2048`
* Now, I will mount another partition under `/media`:\
  `root@server ~]# mount /dev/sdb6 /media/`
  * Writing data under `/media` – see on which partition it will be written? It will be on the last mounted one (`/dev/sdb6`).
*   Unmount:

    text

    ```
    root@server ~]# umount /media/
    root@server ~]# df -h
    ```

    OR\
    `root@server ~]# umount /dev/sdb6`

&#x20;

**Auto mounting**

* Reboot: After a reboot, the mount (`/media`) is gone. We need a way for it to happen automatically at boot.
* Lab: Anything you do manually without automation.
* **Mounting problems:**
  * Device names can change in the machine. For example, `sda1` might become `sdb1` after a reboot.
  * Imagine a movie lover with many drives. If the device name changes, the OS can't mount `sda1` because it doesn't exist anymore.

**Solution:**

* Use UUID (Universal Unique Identifier) – a number that uniquely represents the partition.
  * Why? Because even if someone changes the partition name, the UUID remains the same, so the mounting process won't fail.
* Mount using UUID under any directory. The device name may change, but the UUID persists.
  * It's better to rely on something that identifies the partition, not the name, because the name can change.
* Therefore, if you rely on the name (something that identifies the partition by its name), it might change.
* Mount using UUID: `mount UUID /mount_point` – and this is the best way.

&#x20;

* The configuration files are under this location: `/etc/fstab` (File System Table).
  * This file determines which partitions will be mounted when the machine boots.

**Syntax of fstab:**

text

```
dev  mount_point  filesystem_type  mount_options  dump_order  fs_check_order
```

* `dev`: e.g., `/dev/sda1`
* `mount_point`: e.g., `/media`
* `filesystem_type`: e.g., `ext4`
* `mount_options`:
  * `rw` (read\_write)
  * `ro` (read only)
  * `noexec` → don't execute any binaries from this mount point (like scripts).
  * `exec` → by default, any binary can be executed.
* `dump_order`: `0-9`
  * I can configure backup from a partition when the machine is booting.
  * `0` = no dump.
  * `1` = first partition you can dump.
  * `2` = second partition that you can dump.
* `fs_check_order`: `0-9`
  * Check partition order (e.g., after a power failure, the system checks partitions in order).
  * Use `0` for no check.
  * `1-9`: The order of checking. The first partition gets 1, the second gets 2, etc.
  * If two partitions have the same number, they will be checked at the same time (the first one written in the file gets checked first before the second).

**LAB: Persistent\_Mounting\_Using\_FSTAB**

* `mkdir /work`
* `nano /etc/fstab`
* `mount -a`

Any mount configured in `/etc/fstab` will be attempted to mount with this command.

Mounting concept, mounting Lab, auto mounting, Persistent mounting using FSTAB, LAB: Persistent\_Mounting\_Using\_FSTAB

&#x20;

Therefore, errors may occur if you wrote the wrong partition name or something is missing.

* `df -h`
* `cat /etc/mtab` – Shows which partitions you have mounted.
  * It has the same syntax as `/etc/fstab`.
* `mount -a`
  * Mounts everything in `/etc/fstab` that is not already mounted.
  * If a partition is not present in `/etc/fstab`, it won't be mounted, and it will also ensure it's not mounted elsewhere.
* We have two files: the system reads `/etc/fstab` when the machine boots, and another file (`/etc/mtab`) reflects the current mounts.
* When you want to delete a partition, make sure it isn't in `/etc/fstab`.
  * Lab: Double-check.
* `nano /etc/fstab`
* `mount -a`
* `cfdisk /dev/sdb`
* `root@server ~]# fdisk -l /dev/sdb`

&#x20;
