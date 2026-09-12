# Inodes, Hard Links and Soft Links, Disks, and Partition Naming Conventions

What is inode number in Linux?\
Notes in inodes.\
What happens with the inode number when you copy, move or delete a file on the same filesystem?\
Hard links and soft links.\
Disks and partition naming conventions.

`ls -li` – shows the inode number for files.\
`df` – shows information about partitions.\
`df -h` – `-h` stands for human readable; it shows space in a way humans can understand.\
`df -i` – it will display the following information:

Filesystem – The name of the file system.\
Inodes – The total number of inodes on the file system.\
IUsed – The number of inodes that are currently in use.\
IFree – The number of inodes that are currently free.\
IUse% – The percentage of inodes that are currently in use.\
Mounted on – The mount point of the file system.

&#x20;

```
┌─────────────────────────────────────────────────────────────┐
│                       df -i Output                          │
├─────────────┬─────────┬──────┬───────┬───────┬──────────────┤
│ Filesystem  │ Inodes  │ IUsed│ IFree │ IUse% │ Mounted on   │
├─────────────┼─────────┼──────┼───────┼───────┼──────────────┤
│ /dev/sda1   │ 6553600 │186775│6366825│ 3%    │ /            │
├─────────────┼─────────┼──────┼───────┼───────┼──────────────┤
│ /dev/sdb1   │12582912 │139449│1244346│ 2%    │ /data        │
└─────────────┴─────────┴──────┴───────┴───────┴──────────────┘
```

In this example, the root file system (/) has 6,553,600 inodes, of which 186,775 (3%) are in use, and the /data file system has 12,582,912 inodes, of which 139,449 (2%) are in use.

The kernel deals with user ID and group ID numbers, not the names (names are for people to make it easier).

***

&#x20;

* The kernel deals with user ID and group ID numbers, not the names (for people to make it easier).
* The kernel deals with files based on numbers. The number represents the file.

text

```
    BEFORE FORMAT (Empty Land)        AFTER PARTITION (Farmland)
    ┌─────────────────────────┐        ┌─────────────────────────┐
    │                         │        │┌──────┬──────┬─────────┐│
    │    Raw Hard Disk        │        ││ Rice │Cotton│ Water   ││
    │    (No Partitions)      │───►    ││Field │Field │ Supply  ││
    │                         │        ││      │      │         ││
    │  "Like empty land"      │        │└──────┴──────┴─────────┘│
    └─────────────────────────┘        │   Partition 1│2│3       │
                                       └─────────────────────────┘
```

(The text continues with the farming analogy about rice, cotton, and disk preparation.)

***

&#x20;

* MBR: It is the map that allows you to know through it where each partition starts and ends.
* MBR has a partition table (tells you the start and end of each partition) – like iron markers for land.
* Raw disk: If you don’t know start/end of each partition, the disk is just raw.
* Each piece of land must be prepared. You cannot plant on it alone. The land must be prepared for farming.
* File system: Each piece or partition is planted with a specific thing, and the farming method is called the file system.
* To store and retrieve your files, there must be a specific way to deal with it. A file system organizes storing and retrieving files.
* There are many file systems (each has its own way).
* FAT32, NTFS, ext2, ext3, ext4…
* None is better than another; each is good for what you want.
* FAT32: has a problem – maximum file size cannot pass 4 GB. Supports certain partition size (search on Google). Does not support compression or encryption.
* NTFS: supports encryption and compression.

text

```
┌─────────────────────────────────────────────────────────┐
│                   Hard Disk Layout                      │
├─────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────┐    │
│  │ MBR (512 bytes)                                 │    │
│  │  ┌─────────────────────┐                        │    │
│  │  │ Partition Table     │                        │    │
│  │  │ (64 bytes)          │                        │    │
│  │  └─────────────────────┘                        │    │
│  └─────────────────────────────────────────────────┘    │
│                                                         │
│  ┌──────────┬──────────┬──────────┬─────────────────┐  │
│  │ Primary 1│ Primary 2│ Primary 3│ Extended        │  │
│  │  FAT32   │  NTFS    │  ext4    │  Partition      │  │
│  └──────────┴──────────┴──────────┴──┬──────────────┘  │
│                                      │                  │
│                                ┌─────┴─────┐            │
│                                │ Logical 5 │            │
│                                │ Logical 6 │            │
│                                └───────────┘            │
└─────────────────────────────────────────────────────────┘
```

***

&#x20;

* Supports encryption and compression.
* Cluster: includes all storage available. You have a group of servers: a group of machines that do a specific thing like high availability, shared storage, load balancing. You make storage. This storage is called storage pool.
* This means you have more than one disk working together.

text

```
    Inode Table                            Data Blocks in Partition
    ┌──────────────────────────────┐      ┌─────────────────────────┐
    │ Inode# │ File    │ Blocks    │      │ Block 1      Block 2    │
    ├──────────────────────────────┤      │  ┌──┐        ┌──┐       │
    │  101   │ file.txt│ 512,570   │──────┼─►│▒▒│        │▒▒│       │
    ├──────────────────────────────┤      │  └──┘        └──┘       │
    │  102   │ pic.jpg │ 250,251,  │──────┼─► Block 250  Block 251  │
    │        │         │ 500       │      │  ┌──┐        ┌──┐       │
    └──────────────────────────────┘      │  │▒▒│        │▒▒│       │
                                          │  └──┘        └──┘       │
                                          │ Block 500    ...        │
                                          │  ┌──┐                   │
                                          │  │▒▒│                   │
                                          │  └──┘                   │
                                          └─────────────────────────┘
```

* Each partition must know what is stored inside via an index.
* Each partition is divided into blocks, like floor tiles in your house.
* Example: I can tell you I have a file stored in block 512 and another in block 570.
* Every partition has an inode table.
* Partition table: for the whole disk.
* Inode table is specific to each partition; they don’t know each other.
* It stores metadata about files. Its area is small. It does not store files; it stores the inode table.

***

&#x20;

* Format: is the process of preparing land for farming. Technically, you format each partition.
* In reality, you only do this if you buy a new disk or want to change the partition.
* What is the difference between files and directories?
* File: like film, song.
* Directory: folder is a special file that points to other files.

text

```
    Directory Structure                 Different Block Sizes
    ┌──────────────────┐               ┌─────────────────────────┐
    │  /home/user      │               │ Small Blocks (128KB)    │
    ├──────────────────┤               │ ┌─┬─┬─┬─┬─┬─┬─┬─┬─┬─┐   │
    │ file1  → inode101│               │ │▒│▒│▒│▒│▒│▒│▒│▒│▒│▒│   │
    │ file2  → inode102│               │ └─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘   │
    │ photos/ → inode103│               │ Many blocks, some empty│
    └──────────────────┘               └─────────────────────────┘
                                         ▲
    File Types:                          │
    ┌─────────┬─────────┐               │
    │📁 Folder│📄 Document│              │ Large Blocks (1MB)      │
    │🖼️ Image │🎵 Music   │              │ ┌─────────┬─────────┐   │
    │📜 Text   │🔧 Executable│            │ │▒▒▒▒▒▒▒▒▒│▒▒▒▒▒▒▒▒▒│   │
    └─────────┴─────────┘               │ └─────────┴─────────┘   │
                                         │ Fewer blocks, less waste│
                                         └─────────────────────────┘
```

* Each partition divided into blocks (data stored in block). Each block has a number.
* Inode number points to block. Its size is example 128 bytes. This determines block size.
* If you increase block size, number of blocks decreases, and vice versa. Note: only one file stored in each block, so space may be wasted.
* Example: if block size is 128 KB and file size is 5 GB, many blocks will be used, one after another, until file is complete.
* It points to a block with small space. How to store big file? Use many blocks.

***

&#x20;

* It will use many blocks, one after another, until file is complete, but only first one points to file.

text

```
| dir    |
|---|
| 1 ---> x    |
| 2 --->      |
| 3 --->      |
| 4 ---> used |
| 5 --->      |
| 6 --->      |
| 7 ---> y    |
| 8           |
```

* What is inode number in Linux?
* In Linux, when a new file is created, it gets a file name and an inode number. This number is the unique ID for that file.
* As a user, you use file name to access file, but Linux first maps filename to inode number in a database.
* Inode is a data structure that points to the blocks that make the file. Metadata for each file is kept in inodes in a table. Includes:
  * File type (executable, block special, etc.)
  * Permissions (read, write, etc.)
  * UID (Owner)
  * GID (Group)
  * File size
  * Time stamps (last access, last change, last inode change)
  * File deletion time
  * Number of links (soft/hard)
  * Location of file on hard disk
  * Other metadata

***

&#x20;

* Inodes are at start of partition. When you refer to file by name, system searches for correct inode in directory entry.
* Every file has inode number like ID in your country. Impossible for two files in same partition to have same inode number. Different partitions is normal.
* If problem in inode table in one partition, it does not affect other partitions because each has its own inode table.
* If you have big file, it needs more than one inode. First reserved inode is inode number of file, but rest of inodes have flag that they are used.

**What happens with inode number when you copy, move, delete on same filesystem?**

**Copy:**

* Copy in same partition:
  * System books inode number(s)
  * System copies data block and writes in other data block
  * System makes pointer pointing to file and its inode in new directory
* Copy in different partition:
  * System books inode number(s)
  * System copies data block and writes in new data block in new partition
  * System makes pointer pointing to file and its inode in new directory

**Move:**

* Move (cut) in same partition:
  * (Text seems incomplete in PDF)

***

&#x20;

&#x20;

```
    Hard Links (Same Inode)          Soft Links (Different Inode)
    ┌─────────────────────────┐     ┌─────────────────────────┐
    │  Inode 600              │     │  Original File         │
    │      │                  │     │  Inode 400 → Blocks    │
    │      ▼                  │     │      │                 │
    │  Data Blocks            │     │      │                 │
    │  ┌─────────────┐        │     │      ▼                 │
    │  │ File Content│        │     │  ┌─────────────┐       │
    │  └─────────────┘        │     │  │ File Data   │       │
    │      ▲                  │     │  └─────────────┘       │
    │      │                  │     │                       │
    │  ┌─────┬─────┐          │     │  ┌─────────────────┐  │
    │  │file1│file2│          │     │  │link.txt         │  │
    │  └─────┴─────┘          │     │  │(points to name) │──┘
    │  Both point to          │     │  └─────────────────┘
    │  same inode 600         │     │  "Shortcut to file1"
    └─────────────────────────┘     └─────────────────────────┘
```

* Inode number → points to blocks that make file.
* Example: `/usr x=500` means points to file named something with inode number something.
* Move (cut) in different partition:
  * System books inode number(s) on new partition
  * System copies data block and writes in new data block in new partition
  * Inode number… (text incomplete)

***

&#x20;

* Soft link associates one file with another. If original file removed, soft link points to nothing.
* Create soft link: `ln -s file1 softlink`
* Hard link associates multiple files to same inode. If original removed, still access data through linked file.
* Create hard link: `ln file1 hardlink`
* Soft link (shortcut) – inode number points at data block that makes file, and file has name.

Example: `file1 =400, file2=file1`\
`file2` points at `file1` (file name)\
but `file1` points at inode number.

* Data block has inode number and has name (file name). Inode number and file name have a file name. I will reach file with new name, it gets one inode to store it, new name points to old file name. Shortcut or soft link in same file system.
* Shortcut or soft link in different file system or partition:\
  Example: in my partition create file named `file2`, but `file2` points at `file1` in other partition.\
  Note: `file2` points at name, not inode, in other partition.\
  So you need to write full path or absolute path.
* `ln -s` the absolute path (for source file) absolute path (for shortcut)\
  Example: `ln -s /root/file1 /usr`\
  `ls -ll /usr/file1`

***

&#x20;

* Hard links: Data block has name and inode number.\
  Example: `600(inode)=file1, 600(inode)=file2` – if you remove one, other still works.
* Same inode points to two files (two file names). Two files have same inode number.
* Cannot work on more than one partition because data blocks exist on one partition; must be on same partition.
* `ln file1 file2` creates link, they have same inode number.
* `rm -rf file1` – you can still point to data block on disk.

**Disks and Partitions Naming Conventions**

* Total size of MBR is **512 bytes**.
* Partition table is part of MBR; size 64 bytes.
* Info for each partition stored in 16 bytes.
* Maximum partitions for disk is 4 – not enough.
* Solution: maximum partitions 4. Special type called ‘extended’. Inside extended, logical partitions. Their numbering starts from 5.
* Four primary partitions. One can be extended.

**Naming Conventions**

* Partition names in Linux different from Windows.
* Motherboard connects more than one disk at same time.
  * First hard disk: `hda` (a = first)
  * Second hard disk: `hdb` (b = second)
  * Example: `hdc` = hard disk number 3.

***

&#x20;

* You know on same disk there are partitions.\
  Example: `hdc1` = first partition on third hard disk.

Example disk layout:

text

```
sda
├─sda1
├─sda2
├─sda3
└─sda5
```

Exercises: identify names like `hdb`, `sde`, `vda3`, etc.

**Commands (`lsblk`)**

* `lsblk` shows details about block devices (hard disks) except RAM disk. These are files representing devices connected to PC.
* Drives can read CD/DVD. Example devices: `sr0` (DVD).

text

```
    Linux Disk Naming Convention       lsblk Output Example
    ┌─────────────────────────────┐   ┌──────────────────────────┐
    │ Motherboard Connectors:     │   │ NAME   SIZE   TYPE MOUNT │
    │                             │   ├──────────────────────────┤
    │  ┌─hda─┐  ┌─hdb─┐  ┌─hdc─┐ │   │ sda    500G   disk       │
    │  │Disk1│  │Disk2│  │Disk3│ │   │ ├─sda1 100G   part /     │
    │  └─────┘  └─────┘  └─────┘ │   │ ├─sda2 200G   part /home │
    │                             │   │ └─sda3 200G   part       │
    │  Partitions:                │   │ sdb    1TB    disk       │
    │  hda1, hda2, hda3           │   │ └─sdb1 1TB    part /data │
    │  hdb1, hdb2                 │   │ sr0    -      rom        │
    │  hdc1, hdc2, hdc3           │   │   (CD/DVD drive)         │
    └─────────────────────────────┘   └──────────────────────────┘
```

***

&#x20;

* To read disc, machine needs one drive to read disc.
* `ls /dev/sr0`, `ls /dev/cdrom`
* For every device (DVD, CD, CDROM), you should…
* Every device points to `sr0` (softlink). Access devices through `/dev/sr0`.

text

```
    CD/DVD Devices:
    ┌─────────────────────────────┐
    │ /dev/sr0  → CD/DVD Drive   │
    │ /dev/cdrom → Link to sr0    │
    │ /dev/dvd   → Link to sr0    │
    └─────────────────────────────┘
```

<br>

&#x20;

&#x20;
