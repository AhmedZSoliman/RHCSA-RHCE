# Monitoring and managing Linux processes and priorities

## ✅ **Process Concept in Linux**

*   &#x20;

    A **process** is a **program in execution**.

    This means:

    * A **program** is just a file on disk (like `notepad.exe`, `firefox`, `nginx`).
    * A **process** is what happens **after you run that program**.
    * When the program loads into:
      * **RAM (memory)**
      * **CPU**
      * And starts executing instructions\
        → It becomes a **process**.
* **he system controls the users by user ID and controls processes by process ID (PID).**
* **Each process has a parent process ID (PPID).**
* **Why do you track the parent process?**
  * If the child process dies or freezes, you go kill the parent, and the child is considered dead with it.
* **Each process has a number.**
  * `systemd` is the first process that starts on your system in RedHat 7.
    * It takes the number 1 (calls all the other services in the machine).
  * `init` in RedHat 6.
    * It also takes the number 1.

***

* `ps` → Display current running processes in Linux.
* `ps a` → Display all running processes from all terminals.
* `ps aux` → Display all processes running in the system.

Columns:

* **USER** → which user runs this process
* **COMMAND** → name of the process
* **CPU / MEM** → how much CPU and memory it consumes
* **TTY** → if the process is working in the background
* **START** → start time
* **TIME** → CPU time used by the process
* **PTS** → you logged in remotely or from GUI
* `ps -ef` → like `ps aux` but adds PPID of every process.
  * PPID 0 → systemd (parent of all processes)
* `pstree` → Display processes as a tree.
* `pgrep process_name` → find the PID of a running process by its name.
* `kill PID` → close the process in the background.
  * Sends a signal to the process, “please stop,” by default `SIGTERM`.
* `kill -l` → display all signals.
  * You don’t need all signals; mostly 2 or 3 are enough for a sysadmin. Others are for programmers or other tasks.

***

#### **Important signals for system admin**

* **SIGHUP (1)** → reread or reload the configuration file of the process.
* **SIGKILL (9)** → kill the process forcibly.
* **SIGTERM (15)** → politely ask the process to stop.
* `kill -9 PID` → SIGKILL, a deadly signal that kills the process immediately.
* Suppose we have a stupid process we can’t kill. What do we do?
* `pkill process_name` → kills by process name, not PID.
  * Problem: if multiple instances exist, all will be killed. Always use PID to kill processes safely.

***

* **You can open an app from terminal, but it can reserve the terminal:**
  * `firefox` → terminal reserved
  * `Ctrl+C` → terminate process
  * Solution: run app in background → `firefox &` → terminal is not reserved
* `jobs` → display running applications in the background
  * `[1]` → the number of the process in the background
* `fg %1` → bring process number 1 in background to foreground
* `Ctrl+Z` → pause the process
* `bg %1` → run the process in the background

***

#### **Notes about process management**

* **Any process can work in foreground or background (daemon or service).**
* **Service** → waits for requests from users. The word “service” means server.
  * You can create an application and run it as a service even if it doesn’t accept external requests.
  * A service like SSH responds to connections from users outside the machine.
* **Daemon** → a background process invisible to the user but you can see its effect.
* When you terminate a parent by PPID, the children terminate too, but this applies to foreground processes.
* Background processes’ parent depends on where it started.
  * If started by GUI → parent = GUI
  * If started by system boot → parent = systemd
* **Background processes become daemons. If the parent terminates, the process is automatically adopted by systemd.**

***

#### **Linux process priorities**

* **Control priority with nice value.**
* **Nice value:** -20 (highest) to 19 (lowest)
* Default value = 0 → normal priority
* -20 → highest priority → uses more system resources to finish faster
* 19 → lowest priority
* Normal users can set lower priority only. Root can do anything.
* `top` → Display top running processes in real time
  * Shows system uptime, logged-in users, load average (1, 5, 15 min)
  * Tasks → how many processes are running
  * Zombie → kernel cannot know if process is running, uses system resources but not active
  * CPU → us (user consumption), sy (system consumption)
  * `k PID` → kill process from top, can choose signal
  * `q` → quit top
* `nice -n value process` → start a process with specific priority
  * Example: `nice -n 15 firefox`
* `renice -n value PID` → change nice value of one or more running processes
  * Example: `renice -n -20 10254`
* `Ctrl+C` → send SIGTERM (15) to process
* `Ctrl+Z` → pause process, sends SIGINT
* Option `q` in `top` → send SIGQUIT
