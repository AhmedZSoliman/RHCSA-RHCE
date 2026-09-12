# Daemons and Services + system targets

#### **Understanding the Boot Process**

* **What happens when you boot (start) your machine**\
  Here, we will explain the steps from the moment you turn on your computer until it’s fully up and running.

***

*   **1- POST (Power-On Self-Test):**

    When you press the power button, the machine starts turning on. Keep in mind that the battery provides power to the motherboard, which is connected to all the machine’s components.

    The boot instructions start by checking the RAM. The RAM is where the motherboard stores temporary data.

    These boot instructions, often referred to as **BIOS (Basic Input/Output System)** or **UEFI (Unified Extensible Firmware Interface, which is newer)**, are loaded from the RAM.

    The **BIOS/UEFI** contains the fundamental code needed to start up the machine.

    The BIOS/UEFI performs a series of checks known as the **Power-On Self-Test (POST)** to ensure that various hardware components are functioning correctly. This includes checking the CPU, memory, and storage drives to see if there are any issues.

    It tests the minimum hardware required to start the machine.

    * For example, if the CPU or RAM is not installed, it may fail, but something like a printer isn’t essential at this stage.

    Once the POST completes successfully, the BIOS/UEFI begins the process of booting the operating system.

***

*   **2- MBR (Master Boot Record):**

    The system now needs to find the appropriate boot device to continue the boot process.

    This is where the **Master Boot Record (MBR)** comes into play.

    The MBR is a small program stored at the beginning of the storage device (like a hard drive or SSD).

    It contains information about the partitions on the storage device and a small piece of code that identifies the boot loader program. This points to the specific partition on the boot device that contains the operating system files and the boot loader.

    The MBR looks for the boot loader program (responsible for loading the OS kernel). In Linux, the boot loader is **GRUB 2 (Grand Unified Bootloader)**.

    The MBR on the boot device identifies the correct boot partition, allowing the system to execute the appropriate boot loader program to fully boot the OS.

    The boot loader is **GRUB**, and you can boot from a USB stick, a CD, or an installed OS on the disk.

    If the machine cannot find a bootable device, it will try the next available device. If nothing is found, the machine will fail to boot.

    ```bash
    ls /boot  # Lists the files that configure the kernel
    ```

***

* **3- Bootloader loads the kernel**
* **4- Kernel starts the process called systemd (or init)**
* **5- systemd starts all services**

***

#### **Lab Notes**

* **Service:** A process waiting for requests from local or remote users.
* All configuration files are stored under the `/etc` directory.
  * The **GRUB2 configuration file** is stored under `/boot`.
* It’s a general rule in Linux that all configuration files should be stored in `/etc`.
  * To follow this rule, you can create a symbolic (soft) link from the GRUB2 configuration file in `/boot` to `/etc`.
* **The Linux kernel is divided into two main parts:**
  * **Kernel file:** Contains the core of the operating system.
  * **Initial RAM filesystem (initramfs):** Includes modules and drivers necessary to start the system, such as those required to access the hard disk or network.
  * This design keeps the kernel file small.
*   The **GRUB2 configuration file** specifies which kernel and initramfs to load during the boot process.

    ```bash
    ls -lh /boot  # See the size differences
    ```

    * `vmlinuz` → your kernel file

    ![Boot Folder](https://prod-files-secure.s3.us-west-2.amazonaws.com/36b1bbe2-6089-447b-9a24-dba4aaf122a6/4aef7645-0d7a-4dbd-8599-08bca9fa331a/Untitled.png)

***

* When the kernel tries to access the filesystem, it **mounts the filesystem in read-only mode first**.
  * This ensures that the filesystem doesn’t have problems.
  * If there are problems, the boot process will fail.
  * If everything is fine, the filesystem is remounted as **read-write (rw)**.
* The kernel **does not start writing data to the filesystem immediately**; it first checks to ensure everything is okay

#### **Understanding systemd and init**

* The **bootloader calls the kernel**, and the **kernel calls systemd** (RHEL7) or **init** (RHEL6).
* **systemd (RHEL7) / init (RHEL6):**
  * The first process started on the system.
  * Has **PID 1** and calls all other services or processes, like `ssh` or `apache`.

**Difference between systemd and init:**

* **Services** – Their role is to call other processes or services.

***

**init (RHEL6)**

* Works **series-wise**, process by process.
  * Calls a process, waits until it completes or fails, then calls the next one, and so on.
* **How init calls every service:**
  * Every service has an **init script** (like `ssh`, `ftp`), and init uses this shell script to start the service.

***

**systemd (RHEL7)**

* Works **in parallel** – calls all services or processes at the same time.
* **Faster** than init.
* Uses **unit files**:
  * Simple text files, no traditional code.
  * Contain `var=value` settings.
* **Systemd is compatible** with init and upstart.
* With systemd, you have full control over services using the command:

```bash
systemctl
```

*   **systemd and journald** were written at the same time.

    **Logs:**

    * Files where every event on the machine is recorded:
      * User logins
      * User additions
      * System shutdowns
      * User modifications
    * **Bootloader → Kernel → init → call services until rsyslog**
    * Any service that starts **before rsyslog** won’t have its logs available.
    * `journald` collects logs and allows advanced filtering, unlike rsyslog.

```bash
journalctl  # Search and filter logs
```

* Example: Filter logs from 6 AM to 9 AM, or search for logs of a specific service.

***

#### **systemd / init Lab Commands**

**Using systemd**

* **Check status of a service:**

```bash
systemctl status serviceName
# Example:
systemctl status sshd
```

* **Notes:**
  * Services usually end with `d` (daemon – runs in background).
  * The file used to load/start a service is defined in the unit file.
  * Default service status (enabled/disabled) is determined by the vendor.
* **Status meanings:**
  * `Active (running)` → currently running (unrelated to reboot)
  * `Enabled` → starts automatically at boot
  * `Disabled` → does not start automatically at boot
* **Service management commands:**

```bash
systemctl start serviceName       # Start a service
systemctl stop serviceName        # Stop a service
systemctl enable serviceName      # Enable service at boot
systemctl disable serviceName     # Disable service at boot
systemctl is-enabled serviceName  # Check if enabled at boot
systemctl is-active serviceName   # Check if currently running
systemctl restart serviceName     # Restart a service
systemctl reload serviceName      # Reload config files
systemctl mask serviceName        # Prevent service from starting
systemctl unmask serviceName      # Allow masked service to start
```

***

**Using init**

* **Check status of a service:**

```bash
service serviceName status
```

* **Start/Stop a service:**

```bash
service serviceName start
service serviceName stop
```

* **Enable/Disable service at boot:**

```bash
chkconfig serviceName on   # Enable
chkconfig serviceName off  # Disable
```

#### **Systemd Targets**

* A system may have a **GUI** (Graphical User Interface) or **CLI** (Command Line Interface).
* The main interface is **CLI**, but the system can start either CLI or GUI depending on the mode.
* **Systemd** uses **targets**, similar to runlevels in init, to define which mode the system runs in.

***

**Difference between systemd and init targets**

&#x20;

*   **init (RHEL6):** called **runlevels**\
    ![](<../.gitbook/assets/image (124).png>)

    ![Runlevels](https://prod-files-secure.s3.us-west-2.amazonaws.com/36b1bbe2-6089-447b-9a24-dba4aaf122a6/61a0c31a-c368-490f-a784-2fedb70b555f/Untitled.png)
* systemd modes (targets like run\_levels)
*

    <figure><img src="../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

***

**Check and change systemd target**

* **Check the default target (runlevel) of your machine:**

```bash
systemctl get-default
```

* **Change the default target (runlevel):**

```bash
systemctl set-default multi-user.target
```

* This will take effect **at the next boot**.
* **Check current and previous runlevel:**

```bash
runlevel
```

* The first value → previous runlevel
* The second value → current runlevel
* `N` → not available (because there was no previous runlevel on boot)

***

**Change targets without reboot**

* **Switch to a different target while the system is running:**

```bash
systemctl isolate graphical.target
```

* **Power off the system:**

```bash
systemctl poweroff
```

* **Legacy init commands exist in RHEL7 for compatibility:**

```bash
init 6   # Reboot the machine
```

***

**Systemd service files**

* Original systemd service files are in:

```bash
ls /usr/lib/systemd
```

* When you **enable a service**, a **soft link** is created in `/etc/systemd/system/`
* When you **disable a service**, the soft link in `/etc/systemd/system/` is removed
* Example:
  * Services like ssh, ftp, cron exist in `/usr/lib/systemd`
  * If you want them to start automatically at boot, systemd creates a soft link in `/etc`.

***

**Dependency-based boot**

* **systemd is dependency-based:**
  * You can specify that a service runs **after another service**.
  * Example: `serviceX` runs after `serviceY`.

***

**List all systemd units and their status**

* **Display all active units:**

```bash
systemctl list-units
```

* **Display all unit files and their enabled/disabled status:**

```bash
systemctl list-units-files
```

* Status indicators:
  * **Green** → service is running and enabled (starts at boot)
  * **Red** → service is stopped and disabled (does not start at boot)
  * **Static** → service runs during boot to perform a task but doesn’t stay active afterward (e.g., filesystem check)

***

**Init service enabling/disabling**

* **Enable a service:** prefix `S` before the service (e.g., `S01ssh`, `S03apache`)
* **Disable a service:** prefix `K` before the service (e.g., `K02ssh`)
* The numbers (`01, 02, 03`) represent the **sequence order** of starting or stopping services.

***

**Check failed services**

* Use systemd commands to identify any failed services across all servers:

```bash
systemctl --failed
```
