# RAID: Concepts, Levels (0, 1, 5, 6, 10), Creating Arrays, Failure Simulation, Stopping RAID, and Imp

#### **RAID Concept**

* **Stands for Redundant Array of Independent Disks (HW Raid).**
* **Or stands for Redundant Array of Inexpensive Disks (SW Raid).**
* You have a server with a disk, and you write data on it. If the disk fails, you are stuck.
* The problem is not that simple. There is a solution: backup. But the question is, does backup help? I will tell you there is a solution.
* Backup happens at a specific time, not in real time.
* Is there a way to put another disk beside it, so when data is written, it writes to both? So if one fails, the other works.
* We will do **mirroring** instead of writing data on one disk. We write it on two.
* If one of them fails, we remove it and run the other, and we get a new one. The new one will also have mirroring.
* Let's understand the idea from the abbreviation of the thing.
* **Redundant** means something that repeats. **Independent** means separate. **Array** means list. **Disks** means two or more disks.
* You have a group of separate disks (they have no relation to each other) that will create data redundancy for you.
* The idea is simple: you have a server, and I will install a **Raid card** in it. As soon as you apply Raid...
* The disks you will install in the server are connected to it. A **Raid controller** will decide how many times the data is written: one copy, two, or three. It is responsible for writing the data. In short, you configure it and tell it to write data on how many disks.
* This means you want to say that this card is what controls the number of copies that will be written. The answer is yes. It has nothing to do with the operating system, so the operating system is outside of this.
* **The OS writes data only one time and sends it to the Raid controller. The Raid controller looks at its configuration to decide whether it will write the data once, twice, or three times.**
* **Raid controller (Hardware card):** Manages the array or list of disks. The disks are connected to it.
* It is not allowed to fail, even if the power is cut. Therefore, it is connected to a battery and keeps the data on it until it transfers it to the disks. It supports a specific number of disks, like four or six.
* For example, if I have many disks, then install more than one **Raid controller** in the machine.
* When you create an **array**, you cannot combine disks connected to **different controllers**. They have to be on the same controller.
* Every controller has its specific cache and a small CPU on it.
* **Fast speed cache** is implemented on the Raid controller. It is a storage (with a limited space) but fast for writing. When you write data, it writes to the cache first, and then it transfers what is written on the cache to the disks at its speed.
* **Fast speed cache** has a small space. While data is being written, it transfers what is written from it to the disks.

***

* **There is a way to implement Raid, but in software.**
* **SW Raid:** It will do the same thing that hardware does.
* But it won't have memory like hardware, of course. Because there are things in hardware you won't find in software, like the cache and the battery that keeps data when the power is cut. It saves some money.
* **HW Raid** is the better option between them.

#### **RAID Levels (RAID 0, RAID 1, RAID 5, RAID 6, RAID 10)**

* **Raid levels:** There is more than one way it works, and when it works, it does a specific thing. We call it a level.

***

* **RAID 0 (does striping):** You have one disk or more.
* **Striping:**  divide the data across the disks you have. Therefore, I do not have **redundancy**.
  * If one of the disks fails, your entire data is gone. The usable space for you is the total of all disks. I use all the disks as if they are one disk, completing each other.
  * For example, if you want to read from the disk, you will find it reads a piece of data from one disk and a piece from another disk, and so on.
  * So, the process of reading is very fast, and writing is also very fast because it reads or writes from three disks at the same time, for example. The more you increase the number of disks, the faster the reading and writing.
  * Why? Because a disk has a limited read speed. But when you have more than one, the load is distributed among the disks.
  * For example, if you want to write 50 GB, it will divide the 50 between the disks: 25 on the first disk and 25 on the second disk.
  * **Raid:** You can start with one disk, then when you finish, add another disk, then another, and so on.
  * **Minimum disks used with RAID 0: 1. Maximum: unlimited.**
  * **Example:** We have 8 disks, each disk size is 5 GB. The usable space is 8 \* 5 = 40 GB. It takes the disks and combines them, giving you one very large disk.

***

* **RAID 1 (does mirroring):** This means what you write on one disk, you write on another disk as an exact copy.
* The process of writing is not very good. The process of reading is excellent.
* **Minimum disks used with RAID 1: 2. Maximum: unlimited.**
* If any disk fails, as long as at least one disk is still there, it's fine because you have more than one disk with an exact copy of the data.

***

* **RAID 5 (does striping with distributed parity):** Parity (like a checksum) is used to return missing data.
  * In simple terms, you have a disk with part of the data, and another disk with another part. If one disk fails, the data on the disk that didn't fail and the parity data on another disk are used to recover the data on the failed disk.
  * The process of parity is an **XOR** calculation. For example, if a piece of data named "Ahmed" is on one disk and a piece named "Mohamed" is on another, an XOR operation happens. So, if a piece of data is lost from a disk for any reason, you can get it back.
* **Minimum disks used with RAID 5: 3. Maximum: unlimited.**
* The process of writing is not great and not bad. The process of reading is reasonable.
* Because it writes part of the data on one disk and part on another.
* It allows one disk to fail. So, what do you do if a disk fails? Remove it from the array, add a new disk to the array, and put the missing data on it using parity and the data on the other disks.
  * It will take time because it will do calculations on, for example, the other disks to return the missing data to the new disk.

***

* **RAID 6 (does striping with dual parity):** It makes two parity copies.
* **Minimum disks used with RAID 6: 4. Maximum: unlimited.**
* It allows **two disks** to fail.
* When you do it, always try to use an even number of disks.

***

* **RAID 10 (RAID 1+0):** Uses RAID zero and one, or the other way around.
* **Minimum disks used with RAID 10: 4. Maximum: unlimited.**
* When you do it, always try to use an even number of disks.
* For example, it takes two disks and does **mirroring** on them. Then it takes two more disks and does **mirroring** on them.
  * **Example:** Disk0 is down. Will there be a problem? No, because there is a copy of the data on disk number 1. When will there be a problem? Only if disk number 1 also fails, because then part of the data is lost.
  * For example, if the two disks in the same RAID fail, then you are stuck.

***

* **Raw space:** The space before you put it into an array.
* **Usable space:** How much you get after you put it into an array.
* When you have a **Raid array** made of, for example, three disks (regardless of the level), and you make a **Raid array**, you will create a file system. On which one will you write first? The first, second, or third?
  * What will happen is that you will mix the three in a blender and create a new device called a **Raid device**. After that, you will deal with the **Raid device**, which is responsible for writing data on which disk. You have no relation to the individual disks.
  * Therefore, when you format, you format the **Raid device (virtual device representing all disks)**.
  * The disks may be virtual (not physically real). You will still create them and tell the device, "You are responsible for these disks."

***

* The implementation of a **Raid device** (software RAID) is called `mdadm`, or Multiple Disk Administration tool.
* The `mdadm` tool allows you to create and manage a RAID device, or **create a virtual device, set a RAID level for it, and specify which devices it is responsible for.**
  * And you also create a **file system** for it.
* You must prepare the environment and create more than one disk to work on this topic.

<br>

#### **Create RAID Array, Simulate RAID Failure, Stop RAID, Stop RAID with Data Removal**

***

#### **Create RAID Array:**

*   **Before you delete any partition, make sure it is not in `/etc/fstab` and is not mounted.**

    bash

    ```
    [root@server ~]# cat /etc/fstab
    [root@server ~]# lsblk
    ```
*   **I will use the disks to create a RAID array and a RAID device (a virtual device representing all disks).**

    bash

    ```
    [root@server ~]# mdadm --create /dev/md0 --level=5 --raid-devices=3 /dev/sdb /dev/sdc /dev/sdd
    ```
*   **To know the status of the RAID array:**

    bash

    ```
    [root@server ~]# cat /proc/mdstat
    ```

    Output example:

    text

    ```
    Personalities: [raid6] [raid5] [raid4]
    md0: active raid5 sdd[4] sdc[2] sdb[1] sda
    62862336 blocks super "1.2" level 5, 64k chunk, algorithm 2 [4/4] [UUUU]
    ```

    **\[4/4]** means you can use all four disks out of four.

    **\[UUUU]** means all four are **up**.
*   **To create a file system on the RAID device:**

    bash

    ```
    [root@localhost ~]# mkfs.ext4 /dev/md0
    ```
*   **To mount the RAID device:**

    bash

    ```
    [root@server ~]# mount /dev/md0 /media/
    ```
*   **If you want to know the size of the RAID device after creating the array, use `df -h`.**

    This virtual device combines all the disks together, and its size is determined by the RAID level.

***

#### **Simulate RAID Failure:**

* **I will remove a disk from the disks (simulating a disk failure).**
* **Note for VMware:** It may not detect when a disk is removed. This is a problem. Whenever you remove or add hardware, you should shut down the machine.
*   **Check the status to see that a disk has failed:**

    bash

    ```
    [root@server ~]# cat /proc/mdstat
    ```
*   **Add a disk to the RAID device to replace the failed or removed one. The `-a` option means add.**

    bash

    ```
    [root@server ~]# mdadm /dev/md0 -a /dev/sdd
    ```
*   **Rebuild the array:** It will check what data was written when the disk was missing or failed. Using **parity** and the data parts on the other disks, it will try to create the data on the new disk.

    bash

    ```
    [root@server ~]# cat /proc/mdstat
    ```

***

#### **If You Want to Stop RAID (Without Data Loss):**

*   **Unmount the directory, for example `/media`:**

    bash

    ```
    [root@server ~]# umount /media
    ```
*   **Stop the array. Stopping it does not mean the data is lost. You can tell it to work again later.**

    bash

    ```
    [root@server ~]# mdadm --stop /dev/md0
    ```
*   **Check the status of the RAID array:**

    bash

    ```
    [root@server ~]# cat /proc/mdstat
    ```
*   **Can you reassemble it again? Yes, you can. Look for an existing array and reassemble it.**

    bash

    ```
    [root@server ~]# mdadm --assemble --scan
    ```

    **Result:** It will say something like: `mdadm: /dev/md/0 has been started with 3 drives`.
*   **After executing the previous command, everything works normally again.**

    bash

    ```
    [root@server ~]# cat /proc/mdstat
    ```
* **When you stop the RAID array, it does not remove the data, and the disks remain as they are. You can gather the disks again under the RAID device, but you won't see the RAID device that managed the array.**
*   **To ensure auto-mount happens when the machine boots, you must add the mount to `/etc/fstab`.**

    Edit the file:

    bash

    ```
    [root@server ~]# vim /etc/fstab
    ```

    Example content:

    text

    ```
    # /etc/fstab
    # Created by anaconda on Tue Aug  2 19:47:54 2016

    # Accessible filesystems, by reference, are maintained under '/dev/disk'
    # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info

    /dev/mapper/rhel_server-root /       xfs     defaults        0 0
    UUID=c5d68544-7bca-48cd-8675-64b6dc28436c /boot   xfs     defaults        0 0
    /dev/mapper/rhel_server-home /home   xfs     defaults        0 0
    /dev/mapper/rhel_server-swap swap    swap    defaults        0 0
    /dev/md0    /media  ext4    defaults        0 0
    ```

    Then:

    bash

    ```
    [root@server ~]# umount /media
    [root@server ~]# mount -a
    ```

***

#### **If You Want to Stop RAID with Data Removal:**

* **When you stop the RAID array with data removal, it will remove the RAID device. If you later do a scan and assemble to collect the disks together again, they will be under a new RAID device.**
*   **The `scan and assemble` command looks at the disks in the block devices. It searches for a block called a `superblock` on each disk. This superblock has information about which RAID array the disk belongs to.**

    **Superblock:** Written on each disk, it says which RAID array the disk is a member of.
*   **If you want to delete the data on the disks and not gather them again, delete the superblock on the disks.**

    bash

    ```
    [root@server ~]# mdadm --zero-superblock /dev/sdb
    [root@server ~]# mdadm --zero-superblock /dev/sdc
    [root@server ~]# mdadm --zero-superblock /dev/sdd
    ```

    Now, if you do `scan and assemble`, it will not know which RAID array these devices were members of.

<br>

#### **SW Raid on Partitions**

* **SW Raid is more flexible than HW Raid.**
* **SW Raid allows you to put partitions or disks in a raid array.**
  * **Example:** You can take, for instance, two partitions (not the entire disk) and make a RAID array from them.
  * But the idea: If the disk containing the partition fails, you are stuck. For example, with **RAID5** using three partitions and a fourth for parity.
  * Therefore, it is better to have separate disks. Take, for example, the first partition on the first disk and the first partition on the second disk, and make a RAID array from them.
* **HW Raid (Raid controller) allows you to put only disks, not partitions, in a raid array.**
* **Offloading:** This means that one thing takes the work away from another.

***

#### **Implement SW Raid on Partitions**

* **`lsblk`** command displays the disks and their partitions.
* **`fdisk disk_location`** is used to make partitions on the disk.
  * Example: `fdisk /dev/sdb`
*   **How can you change the system ID of a partition (change the partition type)?**

    * The type of the disk: **Linux**.
    * But I need to put them on a partition of the type **Linux raid autodetect**, because I will put a file system on it like ext4 or xfs.

    Example output before change:

    text

    ```
    Device Boot    Start      End    Blocks   Id  System
    /dev/sdb1      2048  10487807  5242880   83  Linux
    ```

    * **Change the partition type and ID of the partition:**
      * In `fdisk`, press `t` to change a partition type.
      * You will see a list of known partition types.
      * Look for **`Linux raid autodetect`** (usually code `fd`).
      * Select it.

    List of partition types (partial):

    text

    ```
    DOS (FAT)
    NTFS
    Sleep
    > FAT32
    > FAT32
    > FAT

    Novell Netware
    DiskSecure Mult
    PC/IX

    Old Minix

    Minix / old Linux

    BSDI swap        fb
    Boot Wizard hid  fc
    Acronis FAT32 L  fd
    Solaris boot     fe
    Solaris          ff

    VMware VMFS
    VMware VMKCORE
    Linux raid auto  <- Press this
    LANstep

    BBT
    ```

    * **This partition is now ready to use as a Raid member.**

    Example output after change:

    text

    ```
    Command (m for help): p

    Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x0c5166ca

    Device     Boot    Start      End    Blocks   Id  System
    /dev/sdb1          2048    10487807  5242880   fd  Linux raid autodetect
    ```

    *   **`partprobe disk`**

        text

        ```
        partprobe [-d] [-s] [devices...]

        DESCRIPTION

        **partprobe** is a program that informs the operating system kernel of partition table changes, by requesting that the operating system re-read the partition table.

        OPTIONS

        | TAG | DESCRIPTION |
        | --- | --- |
        | **-d** | Don't update the kernel. |
        | **-s** | Show a summary of devices and their partitions. |
        | **-h** | Show summary of options. |
        | **-v** | Show version of program. |
        ```
    * Run: `partprobe /dev/sdb`
    * **Apply the steps above to all your disks if you want to make them part of a RAID array.**

***

*   **Example: Here we create a SW raid device (create raid array). We define the member raid devices.**

    text

    ```
    [root@server ~]# mdadm --create /dev/md9 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
    ```
*   **To see the status of the raid array:**

    text

    ```
    watch cat /proc/mdstat
    ```
*   **Make a format for the raid device, not the partitions:**

    text

    ```
    mkfs.xfs /dev/md0
    ```
*   **Mount the raid device:**

    text

    ```
    mount /dev/md0 /media
    ```
* **Note: If the partitions have different sizes, with RAID level one (mirroring), it will take the smallest one. The array size will be based on the smallest partition.**

<figure><img src="../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

* **Note: Do not use a partition or disk that is already a member of one RAID array in another RAID device. In other words, you cannot use the same partition or disk for two different raid devices. That is not allowed.**
* **Note: If you install the OS on RAID and you make a raid device for disks or partitions, what will happen when you reboot the machine? The name of the raid device might change.**
  * For example, if you named it `md0`, after a reboot you might find it changed to `md127`.
  * Developers said numbers 0-127 for raid arrays are reserved for raid arrays (raid devices) during OS installation.
  * RAID arrays created **after** OS installation start from 127.
  * Here is a problem: when you come to mount, you will find the raid device has changed its name.
    * **`blkid`** command displays the ID of the devices.
    * **Try mounting by using the ID of the device, not the name of the device, in `/etc/fstab` to avoid this problem.**

<br>
