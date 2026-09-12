# Linux Boot Process  and Recovering The Root Password

**How to start a Linux boot process?**

The first method is to start a Linux device which is in a stopped state.

The second method is to reboot the running OS.

<figure><img src="../.gitbook/assets/image (123).png" alt=""><figcaption></figcaption></figure>

***

#### **1st step**

**When you press the power button, the machine starts.**

**Note that all components are connected to the motherboard, and the battery provides power to the hardware of the machine.**

**Boot instructions start by checking the RAM, which is the place where the motherboard stores data.**

**These boot instructions, often referred to as the BIOS (Basic Input/Output System) or UEFI (Unified Extensible Firmware Interface, the newer version), are loaded from the RAM.**

**The BIOS/UEFI contains the fundamental code needed to start up the machine.**

**The BIOS/UEFI performs a series of checks known as the Power-On Self-Test (POST), to ensure the various hardware components attached to the device are functioning correctly.**

**If POST fails, the OS will not be operable.**

**This includes checking things like the CPU, memory, and storage drives.**

**It tests the minimum hardware required to start the machine.**

**For example, if a CPU or RAM is missing, other non-critical devices like the printer are not required at this stage.**

**Once the POST is successfully completed, the BIOS/UEFI can then begin the process of booting up the operating system.**

**The system will not proceed to the second stage or step of the boot process until POST is successful.**

***

#### **2nd step**

**The system needs to find the appropriate boot device to continue the boot process.**

**This is where the Master Boot Record (MBR) comes into play.**

**The MBR is a small program stored at the beginning of the device's storage (like a hard drive or SSD).**

**The MBR contains information about the partitions on the storage device, as well as a small piece of code that can identify a boot loader program for the specific partition on the boot device that contains the actual operating system files and boot loader.**

**The MBR will look for the boot loader program (responsible for loading the OS kernel). In Linux version 8, the bootloader is called the Unified Bootloader GRUB 2.**

**The MBR on the boot device identifies the correct boot device partition, which then allows the system to find and execute the appropriate boot loader program to fully boot the operating system.**

**Bootloader is called GRUB.**

**You can boot from a CD, an existing OS on the disk, or a USB flash drive.**

**If the device cannot boot, it will try the next available bootable device. If none are found, the system will not boot.**

**Or:**

**The next stage after POST is the boot loader. After a successful POST test, the BIOS loads and executes the boot code from the boot device, which is located in the first sector of the hard disk.**

**In Linux, this is located in the /boot file system.**

**The boot loader provides the user with a boot screen, often with multiple options to boot into, such as MS Windows OS or Ubuntu in a dual-boot system.**

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/36b1bbe2-6089-447b-9a24-dba4aaf122a6/a2840428-492b-4bf8-8b8c-a384a56df8a7/image.png)

**Once the selection is made at the boot screen, the boot loader loads the kernel into memory.**

**A popular example of the bootloader is GRUB2 (Grand Unified Bootloader version 2), and it is now the primary boot loader for most Linux distributions.**

**After the selected kernel is loaded into memory, it is usually decompressed.**

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/36b1bbe2-6089-447b-9a24-dba4aaf122a6/a2c1820e-732f-4646-8ee5-94afa2a8238e/image.png)

**The kernel is then loaded into memory and starts executing.**

**The kernel starts hardware and memory management tasks.**

***

#### **3rd step**

**The kernel looks for an init process to run, which sets up the user space and the processes needed for the user environment.**

**The init function calls the systemd daemon.**

**Systemd is responsible for bringing the Linux host to a usable state.**

**Systemd is responsible for mounting file systems and starting and managing system services. Systemd is the universal standard these days, but not too long ago, another initialization process called System V init was used.**

**It is also called SysV. For example, this was used in RHEL 6 or CentOS 6 distributions.**

**One of the key advantages of using systemd over SysV init is that it reduces system startup time by parallelizing the startup of services.**

**To check the init system used, run an ls -l command against the file /sbin/init.**

**If it's a systemd-managed system, you will see a pointer to /lib/systemd/systemd.**



<table data-header-hidden><thead><tr><th width="40"></th><th></th><th></th></tr></thead><tbody><tr><td>1</td><td><code>systemctl get-default</code></td><td>Display the Default Target for the machine</td></tr></tbody></table>

<table data-header-hidden><thead><tr><th width="40"></th><th></th><th></th></tr></thead><tbody><tr><td>2</td><td>`systemctl list-dependencies</td><td>grep target`</td></tr></tbody></table>

<table data-header-hidden><thead><tr><th width="40"></th><th></th><th></th></tr></thead><tbody><tr><td>3</td><td><code>systemctl isolate emergency.target</code></td><td>Change to a different Target, like <code>emergency.target</code>. After entering <code>emergency.target</code>, we will check the Default target using <code>systemctl get-default</code>. The Default is still <code>graphical.target</code>. To change it, reboot and in the Boot Process phase, in the Kernel Menu, we press <strong>e</strong> for interrupt, open the File, go to the line starting with <code>$root</code>, write <code>Systemd.unit=emergency.target</code> above it, and then press CTRL+X</td></tr></tbody></table>

| **Recovering root password** |   |   |
| ---------------------------- | - | - |

<table data-header-hidden><thead><tr><th width="40"></th><th></th><th></th></tr></thead><tbody><tr><td>1</td><td><code>reboot</code></td><td>Reboot and then press <strong>e</strong> for interrupt</td></tr></tbody></table>

<table data-header-hidden><thead><tr><th width="40"></th><th></th><th></th></tr></thead><tbody><tr><td>2</td><td><code>rd.break</code></td><td>Go to the last line written in Linux, then write the command and press <strong>CTRL+X</strong></td></tr></tbody></table>

<table data-header-hidden><thead><tr><th width="40"></th><th></th><th></th></tr></thead><tbody><tr><td>3</td><td><code>mount -o remount,rw /sysroot</code></td><td>Change the mount (r) only to (rw) for the <code>sysroot</code> file</td></tr></tbody></table>

<table data-header-hidden><thead><tr><th width="40"></th><th></th><th></th></tr></thead><tbody><tr><td>4</td><td><code>chroot /sysroot</code></td><td>Change the root file system to <code>sysroot</code>, then press Enter. After that, we can write commands normally</td></tr></tbody></table>

<table data-header-hidden><thead><tr><th width="40"></th><th></th><th></th></tr></thead><tbody><tr><td>5</td><td><code>passwd</code></td><td>Enter the command <code>passwd</code> to set a new password</td></tr></tbody></table>

<table data-header-hidden><thead><tr><th width="40"></th><th></th><th></th></tr></thead><tbody><tr><td>6</td><td><code>touch /.autorelabel</code></td><td>Save all changes applied to SELinux in the previous steps. This command relabels the Files Context after changing them, then press Enter</td></tr></tbody></table>

<table data-header-hidden><thead><tr><th width="40"></th><th></th><th></th></tr></thead><tbody><tr><td>7</td><td><code>exit</code></td><td>Exit by writing <code>exit</code> twice</td></tr></tbody></table>

***

If
