# Unix/Linux History & OS Differences

### Why You Should Learn Linux

* **Linux is an Operating System (OS).**
* **Why Linux dominates the server world:**
  * **Security:** Linux is generally more secure than Windows, though no OS is 100% secure.
  * **Stability:** Linux is highly stable and reliable for servers.
  * **Cost-effective:** Linux is open-source and free to use. Anyone can modify its source code and redistribute it. You don’t need to pay to use Linux.

### Linux Distributions

* Linux is **free and open-source**, which has led to a huge number of distributions (**distros**).



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
   * Can be **command-line applications** (CLI) or **graphical user interface applications** (GUI).

<figure><img src="../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

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





