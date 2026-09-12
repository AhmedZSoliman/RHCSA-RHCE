# Linux file system structure and navigation basics.

## **The File System Hierarchy (How Linux Organizes Files and Directories)**

A file system is a method an operating system uses to store, organize, and manage files and directories on a storage device.

File systems are a crucial part of any operating system, providing a structured way to store, organize, and manage data on storage devices such as hard drives, SSDs, and USB drives. Essentially, a file system acts as a bridge between the operating system and the physical storage hardware, allowing users and applications to create, read, update, and delete files.

**Golden rule:** In Linux, everything is a file.

**Golden rule:** The system deals with users through numeric IDs, not names. The same applies to services, processes, or applications.

A directory is like a folder in Windows.

Ask yourself this question: Is there a C drive when you partition a Windows OS? Windows automatically chooses a virtual name like C: (virtual drive name or virtual partition name doesn’t exist physically).

***

### **The File System Hierarchy (Like an Inverted Tree)**

In Linux, `/` is a virtual point—it doesn’t exist physically but represents the starting point of all files.

Let’s find out the purpose of each directory:

* `/` : Root file system; all OS directories are under it (`ls /`).
* `/home` : (Like the Users folder in Windows) Stores the home directories of users.
  * Each user has a directory with their name (user home directory → desktop, downloads, etc.).
  * Example: When a user logs into the system, their folders and files are stored under `/home/user`.
* `/etc` : Contains system configuration files.
  * Example: When you create a user, the user information is stored in configuration files under `/etc`.
  * Each service has its configuration files here.
* `/dev` : Contains special device files used by the system to access hardware.
* `/tmp` : Stores temporary files.
* `/usr` : Shared files between users.
  * Example: Backgrounds, fonts, icons, menus by default.
* `/bin` (binaries) : Stores all user commands (both normal users and superusers can run these commands).
  * Example: `date` command displays the current date and time.
* `/sbin` (super binaries) : Stores system administration commands (only superuser/root can run these).
  * Example: `useradd` command creates a local user on your machine.
* `/var` (variable files) : Stores variable data related to the system like databases, log files, website content.
  * Variable files’ content changes constantly, unlike constant files.
* `/root` : Home directory for the administrative superuser (root).
  * This user is isolated from other users.
* `/proc` : A directory where the kernel keeps information about your machine.
  * Example: Software, hardware, users, processes, services.
  * Only the kernel interacts with this directory.
* `/media` : Example: USB flash drives can be accessed under `/media`.
  * Similar to Windows, when you attach a USB flash drive, the system automatically creates a mount point for it.

**There are three “roots”:**

1. Root user: UID 0
2. `/` : Root file system representing the start of the file system
3. `/root` : Home directory of the root user

* `/boot` : Stores bootloader files (software that allows you to load the OS or kernel).
* `/lib` : Default location for shared libraries in Linux systems.
* `/sys` : Stores system information about your machine.
* `/dev` : Contains special device files used by the system to access hardware.
  * Linux treats hardware devices connected to the server as files (e.g., memory, HDD, mouse).

***

### **How to Access Your File System (CLI or GUI)**

**GUI:** Press Activities (like the Start menu in Windows) → click the Files icon → you will be in the user home directory → to go to the root directory, click the “Other Locations” icon.

**CLI:**

* `ls /` → lists files and directories in any path.
* `ls -l` → lists files and directories with details.
* `tree -L 1 /` → shows the first level of the file hierarchy.

***

### **Types of Files in Linux**

* **File:** A collection of data.
* Example: Analyze the output of `ls -l file_name`.

***

* File : collection of data

| Symbol | Meaning      |
| ------ | ------------ |
| -      | Regular file |
| d      | Directory    |
| l      | Link         |
| c      | Special File |
| s      | Socket       |
| p      | Named Pipe   |
| b      | Block Device |

Regular files Examples.text files,images.\
Directory .like folder.\
Link .shortcut.\
special files Examples .keyboard,mouse.\
Block Device Examples .HDD,memory,iso image.\
Socket ,Named pipe .used for inter-process communication between processes as system admin you will not deal with them.

ls -l\
drwx--- 1 root root 1349 Sep 24 16:18 anaconda-ks.cfg\
drwxr-xr-x. 3 root root 150 Nov 2 02:30 Cloud\_Tools

cd / This command used to change your path and Type ls -l\
lrwxrwxrwx. 1 root root 7 Aug 10 2021 lib -> usr/lib\
lrwxrwxrwx. 1 root root 9 Aug 10 2021 lib64 -> usr/lib64

cd /dev This command used to change your path and Type ls -l\
crw-- -- --. 1 root root 10, 231 Nov 27 10:59 snapshot\
drwxr-xr-x. 3 root root 200 Nov 27 10:59 snd\
brw-rw-- -- + 1 root cdrom 11, 0 Nov 27 10:59 sr0

Let's analyze the entry of This command ls -l file\_name

### **Rules for Naming Linux Files**

* File names should be descriptive.
  * Example: If you take a backup from your system, name the directory something like `Backup_date`.
* File names should use alphanumeric characters (uppercase, lowercase, `@`, `_`).
* Avoid embedded spaces in file names.
* Avoid metacharacters like `* ? > < / | : [] () {}` because they have special meaning in the shell.
* File names are case sensitive.
  * Example: `File` ≠ `FIle` ≠ `fiLe`.
* File names starting with `.` are hidden.
  * Example: `.filename`, `.directoryname`.
* Maximum filename length: 255 characters.

***

### **Navigating the File System**

* `ls` : Displays files and directories.
  * `ls /`
  * `ls /home/ahmed/`
  * `ls /home/ahmed/file.txt/` → If it’s a directory, you can add a slash at the end.
  * Note: Blue = directory, White = file.
* Linux is case sensitive.
  * Example: `N` ≠ `n`, `ahmed` ≠ `AHMED`, `document` ≠ `Document` ≠ `DocUmen`.
* `cat` : Displays the content of a file.
  * `cat file.txt`
* `pwd` : Prints the current working directory.
  * `pwd`
* `cd` : Changes the current working directory.
  * Example: `cd home/` → relative path
  * Example: `cd /home/mostfa` → absolute path
  * `cd -` → switches to the last working directory
  * `cd` or `cd ~` → goes to the user’s home directory (e.g., root or ahmed)
  * `cd ~ahmed` → goes to the home directory of user ahmed
* `touch` : Creates an empty text file.
  * `touch file1.txt`
  * `touch file1 file2 file3`
  * `touch file.txt` → If the file exists, its timestamp is updated

**Paths:**

* **Absolute path:** Full path starting from `/`
  * Example: `cd /home/ahmed/desktop`
* **Relative path:** Based on your current directory
  * `cd .` → current directory
  * `cd ..` → parent directory (one level up)
  * `cd ../../..` → goes three levels up
  * `cd ../../../var/log` → goes three levels up then enters `/var/log`

### &#x20;
