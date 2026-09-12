# Logging and Rsyslogd,Journald

### Linux Logs

* **Any event that happens on the system, whether by processes or the kernel, is stored as a log in log files under `/var/log`.**
* **Any component in the system can create logs.**
* **We use logs for auditing or troubleshooting.**

***

#### Log Services

There are **two main services responsible for logs**:

1. **journald**
   * It is a daemon that collects logs with **more details from the OS**, such as kernel logs or boot logs.
   *   It stores logs under:

       ```
       /run/log/journal
       ```
   * **Note:** If you reboot the system, these logs are deleted.
2. **rsyslogd**
   * It is one of the **oldest Linux daemons** responsible for collecting system logs (from processes and the kernel).
   *   It stores logs under:

       ```
       /var/log
       ```
   * **It receives logs from the `journald` service and writes them to `/var/log`.**

<figure><img src="../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

**Any event or message has priority and severity**

<figure><img src="../.gitbook/assets/image (136).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

## **Configuring Rsyslogd**

<figure><img src="../.gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>

## Linux Logging Commands & Journald Configuration

***

### 1️⃣ Viewing Logs

* Change directory to log files:

```
cd /var/log
```

* View logs with `less`:

```
less messages
```

***

### 2️⃣ Sending a Syslog Message with `logger`

* Basic log entry:

```
logger "Log entry created locally"
```

* Log the process ID too:

```
logger -i "Log entry created locally"
```

* Set a priority for the message:

```
logger -p panic "Log entry created locally"
```

***

### 3️⃣ Rsyslog Configuration

* Rsyslog configuration file:

```
vim /etc/rsyslog.conf
```

* It contains a group of rules that determine how logs are handled.

***

### 4️⃣ Journald

* **`journalctl`**: Command to display logs collected by `journald`.
* **Journal storage**:
  *   Logs are stored under:

      ```
      /run/log/journal
      ```
  * By default, **logs are not permanent**. They are deleted when the system reboots.
* **Configuration files**:

```
vim /etc/systemd/journald.conf
vim /usr/lib/systemd/journald.conf
```

***

#### Storage Options

1.  **Persistent**

    *   Logs are stored permanently in:

        ```
        /var/log/journal
        ```
    * Journald will **not allow logs to exceed 10–15% of the filesystem**.
    * Journald **rotates logs monthly** even with persistent storage.

    **Example setup**:

    ```
    mkdir /var/log/journal
    chown root:systemd-journal /var/log/journal
    chmod g+s /var/log/journal
    vim /etc/systemd/journald.conf
    [Journal]
    Storage=persistent
    systemctl restart systemd-journald
    ```
2. **Volatile**
   *   Logs are stored in:

       ```
       /run/log/journal/
       ```
   * All logs in `/run/log/journal/` are **deleted on reboot**.
3. **Auto**
   * If `/var/log/journal` exists, logs are **stored permanently** there.
   *   If the directory does not exist, logs are stored in:

       ```
       /run/log/journal/
       ```
4. **None**
   * **Do not keep logs**.
   * Logs will **not be written to disk**.
   *   Only **real-time logs** are available with:

       ```
       journalctl -f
       ```
   * Logs are **lost after reboot**.

***

### 5️⃣ Checking Journald Status

```
systemctl status systemd-journald
```

***

### 6️⃣ Useful `journalctl` Commands

* Show all logs:

```
journalctl
```

* Show first few lines:

```
journalctl | head
```

* Show last 10 logs:

```
journalctl -n 10
```

* Show logs since yesterday:

```
journalctl --since yesterday
```

* Show logs for a specific time range:

```
journalctl --since 12:00 --until 22:00
```

* Show logs for a specific user:

```
journalctl _UID=0
```

* Show logs for a specific process:

```
journalctl _PID=0
```

* Show logs for the current boot:

```
journalctl -b
```

* Show logs with specific priority:

```
journalctl -p err
```

* Show logs for a specific service:

```
journalctl -u crond.service
```

* Show logs for a systemd unit:

```
journalctl _SYSTEMD_UNIT=sshd
journalctl _SYSTEMD_UNIT=NetworkManager
```

* Show logs in detailed format:

```
journalctl -o verbose
journalctl -o verbose -p info
```

***

### 7️⃣ Configuring Journald

* Configuration files:

```
vim /etc/systemd/journald.conf
vim /usr/lib/systemd/journald.conf
```



















