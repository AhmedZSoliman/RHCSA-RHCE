# Terminal, users, and basic commands.

#### **Introduction**

* You can access Linux via **GUI** or **CLI (Terminal)**.
  * CLI is where you type commands to interact with the server.
* If there is no GUI, you can use a **TTY (Teletype Terminal)**.
* TTY allows **multiple users to work on the same machine simultaneously**.

***

#### **TTY (Teletype Terminal)**

* Used when you have **physical access** to the machine.
* Multiple black screens (TTYs) allow different users to work simultaneously.
* **Switch between TTYs:**
  * `Alt + Ctrl + Fn` or `Alt + Fn` (F1–F6 for TTY numbers)
* **Return to GUI:**
  * `Alt + Ctrl + F1`
* **Check current TTY:**
  * `tty`
* **Alternative way to change virtual terminal:**
  * `chvt <tty_number>`

**Purpose:** Allow multiple users to use the same machine and run commands independently.

***

#### **Users in Linux**

* **Root user:** Superuser with full permissions.
  * **User ID (UID): 0**
* **Normal user:** Limited permissions.
  * **UID ≥ 1000**
* **System/service users:** UID 1–999 are reserved for system or service accounts.
  * Example: Web server runs as a dummy user with minimal privileges to avoid security risks.
*   **All users are stored in:**

    * `/etc/passwd`

    ```
    cat /etc/passwd
    ```

    * Example: postfix (mail server user) has **no login** and no password.
*   **Check user ID:**

    ```
    id            # for current user
    id username   # for specific user
    ```

***

#### **Switching Users**

*   Switch to another user:

    ```
    su - student   # switch to 'student'
    su - root      # switch to root
    exit           # return to previous session
    ```
* When switching to root, **password is required** because normal users have limited permissions.

***

#### **Basic Commands**

*   **Date and Calendar:**

    ```
    date
    cal
    cal 9 2016     # month year
    cal 2016       # entire year
    ```
*   **List files and directories:**

    ```
    ls
    ```
*   **Eject or insert virtual/physical media:**

    ```
    eject        # eject
    eject -t     # insert
    ```

***

#### **Editing Text Files**

*   **Nano editor:**

    ```
    nano file1.txt
    ```

    * **Inside nano:**
      * `Ctrl + O` → save changes
      * `Enter` → confirm
      * `Ctrl + X` → exit
        * If no changes were made → exits directly
        * If changes were made → prompts for saving steps
