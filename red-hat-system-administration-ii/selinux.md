# SELinux

## Linux Security Concepts

***

### 1️⃣ Types of Security

There are **two main types of security** in Linux:

1. **DAC (Discretionary Access Control)**
   * Based on **permissions**.
   * You give specific permissions to users, which can cause security problems if misconfigured.
   * **Example:**
     * A user has access to a specific file.
     * You give the user **read or write** permission.
     * This type of security is called **DAC**.
2. **MAC (Mandatory Access Control)**
   * Based on **SELinux**.
   * Access is controlled by the system, not by the user.
   * Each service and file has **strict rules**, so users or processes cannot bypass them.

***

### 2️⃣ Linux Components

Linux consists of:

* **Services** (once they run, they become processes)
* **Hardware**
* **Users** (root, service users, normal users)
* **Directories and files**

***

### 3️⃣ Services and Security

* **A service in Linux acts like a user** because every process must run under a specific user account.
* **Important:**
  * If an attacker compromises a service, they gain access to the **user account under which the service runs**.
  * This is why services are run as **non-root users whenever possible**—to limit damage if an attack occurs.
* **Problem:**
  * Any service can access files if other users have **read permission** on most system files.
  *   Example:

      ```
      ll /etc/passwd
      ```
* **Scenario:**
  * Imagine a user attacks a service from outside the server.
  * If the service runs as a normal user, the attacker can **read system files**.
  * To prevent this, we need **stronger security** that isolates each service.

***

### 4️⃣ Need for SELinux

* **Goal:** Each service can only access its own files.
* **Example:**
  * If an attacker tries to read files of other services, they **cannot**.
  * If a user tries to access FTP files, they **cannot access files outside FTP**.
* This isolation is implemented using **SELinux**, which is based on **MAC (Mandatory Access Control)**.

***

### 5️⃣ SELinux Concepts

* SELinux is implemented **inside the kernel**.
* **Primary goal:** Protect user data from system services that have been compromised.
* Linux system can be divided into:
  * **Subjects:** users and services
  * **Objects:** files, folders, ports, sockets
* **Access:**
  * Subjects access objects according to rules.
  * Example:
    * Subject (user) → Object (file)
    * Subject (service) → Object (port or socket)
    * Socket = IP + Port



## SELinux Labels and Domains

***

### 1️⃣ Service Domains

* Each service is placed in its **own domain** (نطاق لوحدها).
* **Goal:** Limit access so a service can only interact with its allowed objects.

***

### 2️⃣ SELinux Context (Labels)

* To implement this, we assign a **label (context)** to each **subject** (user/process) and **object** (file/port).
* Only subjects with the **same type** as the object can access it.

**Example:**

* A normal user can only access directories that have the **same label** as the user.
* SELinux assigns **labels to all system files**.

***

### 3️⃣ Displaying Labels

* **Files:**

```
ls -Z
```

**Example output:**

```
system_u:object_r:admin_home_t:s0
```

* **system\_u** → user type
* **object\_r** → role type
* **admin\_home\_t** → type (main focus for access control)
* **s0** → MLS (Multi-Level Security) or MCS (Multi-Category Security)

> The **type (third part)** is what determines which user or process can access the file.

***

* **Users:**

```
id -Z
```

* **Processes:**

```
ps auxZ
```

* **Open ports:**

```
netstat -Z
```

***

✅ **Key idea:**

* Anything in the OS—**user, process, file, port**—has a **label**.
* SELinux enforces **access control based on these labels**, so services/users can only access what is allowed by their type.





<figure><img src="../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure>

## SELinux Modes and Contexts

***

### 1️⃣ Checking SELinux Mode

* Check the current SELinux mode:

```
getenforce
```

**Modes:**

* **Enforcing:** SELinux policies are applied.
* **Permissive:** SELinux does not enforce policies but logs violations.
* **Disabled:** SELinux is completely turned off.
* Check detailed SELinux status:

```
sestatus
```

* Temporarily set **Permissive mode**:

```
setenforce 0
```

* Temporarily set **Enforcing mode**:

```
setenforce 1
```

> 🔹 **Note:** Temporary changes last until the next reboot.

* To make changes **permanent**:
  *   RHEL7 and newer:

      ```
      vim /etc/selinux/config
      ```
  *   Older versions:

      ```
      vim /etc/sysconfig/selinux
      ```

***

#### 🔹 Notes on SELinux Modes

* No reboot is needed to switch between **enforcing** and **permissive** modes.
* A reboot is required to **disable SELinux entirely** or to enable it from a disabled state.

***

### 2️⃣ SELinux Contexts (Labels)

* Every object in the system (files, directories, processes, ports) has a **SELinux label (context)**.
*   The context consists of **four parts**:

    ```
    user:role:type:level
    ```

    Example:

    ```
    system_u:object_r:admin_home_t:s0
    ```

    * **system\_u** → user type
    * **object\_r** → role type
    * **admin\_home\_t** → type (main focus for access control)
    * **s0** → MLS/MCS (multi-level/multi-category security)
* Display file contexts:

```
ls -Z
```

* Display user context:

```
id -Z
```

* Display process context:

```
ps auxZ
```

***

### 3️⃣ Changing SELinux Contexts

#### Temporary Context Change

```
chcon -t <Context_Type> <File_Path>
```

* Changes the context **until the next reboot or system relabeling**.

**Example:**

```
chcon -t test_t /var/www/html/file.txt
```

***

#### Permanent Context Change

1. Add a file context rule:

```
semanage fcontext -a -t <Context_Type> <File_Path>
```

2. Apply the new context:

```
restorecon -v <File_Path>
```

3. Apply recursively to a directory:

```
restorecon -R -v <Directory_Path>
```

**Example:**

```
semanage fcontext -a -t httpd_sys_content_t "/var/www/html/file.txt"
restorecon -v /var/www/html/file.txt
```

* To remove a permanent context:

```
semanage fcontext -d <File_Path>
restorecon -v <File_Path>
```

***

### 4️⃣ Example: Testing SELinux with HTTPD

1. Install and start Apache:

```
yum -y install httpd
systemctl start httpd
systemctl enable httpd
```

2. Set **permissive mode**:

```
setenforce 0
vim /var/www/html/file.txt
```

3. Access the file in browser:

```
http://127.0.0.1/file.txt
```

4. Change the file context to a different type:

```
chcon -t test_t /var/www/html/file.txt
setenforce 1   # file now will not be accessible
```

5. Restore the default SELinux context:

```
restorecon -R /var/www/html/file.txt
```

> 🔹 **Note:** File contexts are reset to default if the system’s file systems are relabeled at boot.

## SELinux Booleans

***

### 1️⃣ What is a Boolean?

* A **Boolean** in SELinux acts as an **On/Off switch**.
* It controls the behavior of **SELinux policies and rules**.
* SELinux **contexts** are divided into four parts:

```
user:role:type:level
```

* The **third part (`type`)** defines the **SELinux domain or label** applied to processes, files, or services (`httpd_t`, `ftpd_t`, etc.).
* Many Booleans relate to **permissions or actions controlled by the type**.

***

### 2️⃣ Managing SELinux Booleans

#### Temporary Change

```
setsebool <rule> on/off
```

#### Permanent Change

```
setsebool -P <rule> on/off
```

***

### 3️⃣ Useful Commands

* **View all Boolean values:**

```
getsebool -a | less
```

* **View all Boolean values with descriptions:**

```
semanage boolean -l | less
```

* **Modify a Boolean value at runtime:**

```
setsebool ftpd_anon_write on
```

* **Display the Boolean value after modification:**

```
semanage boolean -l | grep ftpd
```

* **Modify a Boolean value permanently (default):**

```
setsebool -P ftpd_anon_write on
```

***

### 4️⃣ Example Scenario

* **Scenario:** Apache service wants to read NFS files.
* By default, SELinux **prevents** this action.
* Solution: Use **predefined Booleans** (rules and policies) to allow Apache access.
* Advantages:
  * Changes are **automatic** and do not require **manual relabeling** for every file.
  * Especially useful for NFS-mounted files.

***

### 5️⃣ Monitoring SELinux Violations

* Install the `setroubleshoot` package:

```
rpm -qa | grep setroubleshoot
```

* This package collects SELinux violation logs.
* View logs:

```
tail /var/log/messages
```

* GUI tool to analyze SELinux alerts:

```
sealert
```

> 🔹 **Note:** Using `sealert`, you can quickly identify and troubleshoot SELinux denials without manually checking logs.

***

