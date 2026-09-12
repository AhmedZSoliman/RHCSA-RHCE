# Unix/Linux History & OS Differences

### Why You Should Learn Linux

* **Linux is an Operating System (OS).**
* **Why Linux dominates the server world:**
  * **Security:** Linux is generally more secure than Windows, though no OS is 100% secure.
  * **Stability:** Linux is highly stable and reliable for servers.
  * **Cost-effective:** Linux is open-source and free to use. Anyone can modify its source code and redistribute it. You don’t need to pay to use Linux.
  * **Powerful CLI:** Linux provides a powerful command-line interface, which helps automate tasks efficiently.
  * **Huge community support:** Linux has a large and active community that provides help, tutorials, and development contributions.

### Unix and Linux History

* **Unix** was one of the first generations of operating systems, developed starting in **1969**.
* The **first version of Unix** was released in **1971**.
* Unix was a major milestone, introducing:
  * A **hierarchical file system** with directories.
  * Concepts like **pipes** and **shell scripts**.
* Unix was written in the **C programming language**, also created at Bell Labs.
* In the early 1970s, Bell Labs made Unix **freely available to universities and research institutions**, which helped it gain popularity as a teaching platform.
* Universities and research labs contributed by **improving Unix, fixing bugs, and creating new programs**.
* Some students developed programs and deployed them on Unix-based systems, leading to projects like **FreeBSD**.
* Bell Labs later introduced a **license** to protect Unix commercially. Companies like IBM, HP, Apple, and Sun bought licenses to use it.
* **Richard Stallman**, a Unix developer, aimed to create a **free OS** anyone could use without a license. He started the **GNU Project** (GNU’s Not Unix), providing free source code for applications, shells, libraries, compilers, etc. However, the GNU project initially **lacked a kernel**, the most essential part of an OS.
* **Linus Torvalds**, a university student, developed a kernel and collaborated with Stallman’s GNU Project. This combination became **GNU/Linux**, commonly referred to as **Linux**.
* Linus based his kernel on **Minix**, a free Unix-like OS. Most applications in GNU/Linux are **compatible with Unix**.
* **Free Software Foundation (FSF):** Ensures Linux source code remains free forever and allows anyone to modify and redistribute it. Protected by the **GPL (General Public License).**

### Linux Distributions

* Linux is **free and open-source**, which has led to a huge number of distributions (**distros**).
*   Distributions are categorized into:

    * **Desktop distros:** Designed for end-users (e.g., Ubuntu, Linux Mint).
    * **Server distros:** Designed for servers (e.g., Red Hat Enterprise Linux, CentOS).



## Linux Architecture (Components)

1. **Kernel (Software):**
   * The core of the operating system.
   * Responsible for **hardware management**, **resource allocation**, and **process control**.
   * End users do not interact directly with the kernel because it is complex and low-level.
2. **Shell:**
   * Acts as an **intermediate layer** between the user and the kernel.
   * A **command interpreter** that:
     * Takes input from the user and translates it for the kernel.
     * Receives output from the kernel and displays it to the user.
3. **Applications (Apps):**
   * Programs that run on Linux.
   * Can be **command-line applications** (CLI) or **graphical user interface applications** (GUI).

<figure><img src="../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

### Differences between Bridge vs NAT vs Host-only (vmware virtual network)

<figure><img src="../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

### **Comparison Between Linux and Windows**

* **Linux** is a free and open-source operating system.
* **Windows** is a commercial operating system.
* Linux has many distributions.
* Windows has limited versions.
* Linux is mostly used through the **Command-Line Interface (CLI)**.
* Windows relies mainly on a **Graphical User Interface (GUI)**.
* Tasks are generally faster to complete in Linux.
* Tasks are generally slower to complete in Windows.
* Users have full control over updates in Linux.
* Windows updates may occur automatically without user control.
* Linux is highly secure.
* Windows is a major target for hackers due to its large user base and vulnerabilities.

***

### **What is the Bash Shell?**

* Bash is a **command interpreter** that acts as an interface between the user and the kernel.
* A shell takes commands from the user, passes them to the kernel, and displays the output.
* RHEL’s default shell is **Bash (Bourne-Again Shell)**, but there are other shells as well.

**Shell Prompt Examples:**

* Normal user:

```
[karim@RHEL ~]$
karim → logged-in user
RHEL → machine hostname
~ → home directory
$ → normal user
```

* Root user (superuser):

```
[root@RHEL ~]#
root → logged-in user
RHEL → machine hostname
~ → home directory
# → root user
```

***

### **Command Syntax**

Format of a command in Bash:

```
command [option(s)] [argument(s)]
```

* **command:** The action you want to perform.
* **option(s):** Modify the behavior of the command (optional).
* **argument(s):** Input values for the command (optional).

Example:

```
[root@RHEL ~]$ usermod -l user1
usermod → command
-l → option
user1 → argument
```

***

### **Accessing the Command Line Interface (CLI)**

1. **From GUI (Graphical Interface)**

<figure><img src="../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>

1. **Remote Access via SSH**

<figure><img src="../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure>

1. **Executing Commands in Bash**
   *   Display TCP/IP network configuration:

       ```
       ifconfig
       ```
   *   Change your password:

       ```
       passwd
       ```

       _(Only root can change other users’ passwords)_
   *   Display current date and time:

       ```
       date
       date --help  # To see all options
       ```

***

### **Working with Files**

*   **Check file type:**

    ```
    file file_name
    ```

    > In Linux, everything is a file. File type is determined by its content.
*   **Display file content:**

    ```
    cat file_name
    ```
*   **View file content page by page:**

    ```
    less file_name
    ```

    > Use arrow keys to navigate; `/keyword` to search.
*   **Display first lines:**

    ```
    head file_name
    head -n 20 file_name  # First 20 lines
    ```
*   **Display last lines:**

    ```
    tail file_name
    tail -n 20 file_name  # Last 20 lines
    ```
*   **View command history:**

    ```
    history
    !!          # Execute last command
    !<number>   # Execute command by history number
    Ctrl + r    # Search command interactively
    ```
* **Tab Completion:**\
  Press **Tab Tab** to auto-complete commands or file names.
* **Slash (/) usage:**\
  Used for specifying absolute paths or long commands.

<figure><img src="../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>





