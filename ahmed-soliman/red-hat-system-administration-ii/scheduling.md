# Scheduling

## **Scheduling Linux Tasks**

Scheduling one-time Linux tasks, Scheduling user recurring Linux tasks, Scheduling system recurring tasks and scripts.

### **More detailed**

Scheduling one-time Linux task.

#### **Motivation for Scheduling**

* **More efficient**
* Care free
* Less expensive

***

Scheduling Linux tasks, Scheduling one-time Linux tasks, Scheduling user recurring Linux tasks, Scheduling system recurring tasks and scripts.

&#x20;

**Scheduling one-time job**\
A one-time job is a task that is scheduled to run only once at a specified time in the future. Unlike cron jobs, which run repeatedly at fixed intervals, one-time jobs execute only once and then disappear.

Scheduling Linux tasks, Scheduling one-time Linux tasks, Scheduling user recurring Linux tasks, Scheduling system recurring tasks and scripts.\
2

\===== Page 3 =====

`at TIMESPEC`

**Examples:**

text

```
at +3 mins
at tomorrow
at teatime
at 4am september 15 2021
at noon +35 mins
```

**Scheduling one-time job - Examples**

text

```
[dolphin@localhost ~]$ at 12:00am tomorrow
warning: commands will be executed using /bin/sh
at> logger "A Midnight test message by at"
at> <EOT>
job 2 at Thu Sep 16 00:00:00 2021
```

text

```
[dolphin@localhost ~]$ echo "Another Midnight test message by at" | at 12:01am tomorrow
warning: commands will be executed using /bin/sh
job 4 at Thu Sep 16 00:01:00 2021
```

**Management of one-time scheduled jobs:**\
`atq` command in Linux is used to display the list of pending jobs that are scheduled using the `at` command.

The `at` command allows users to schedule one-time tasks to run at a specified time.

Scheduling Linux tasks, Scheduling one-time Linux tasks, Scheduling user recurring Linux tasks, Scheduling system recurring tasks and scripts.\
3

&#x20;

text

```
[dolphin@localhost ~]# atq
2    Thu Sep 16 00:00:00 2021 a dolphin
4    Thu Sep 16 00:01:00 2021 a dolphin
[dolphin@localhost ~]#
```

**Job number | Scheduled time | Priority queue | Job owner**

***

**`at` commands**

| Command                   | Functionality                                  |
| ------------------------- | ---------------------------------------------- |
| `atq`                     | Viewing Jobs queue                             |
| `watch atq`               | Updates output of `atq` every 2 seconds.       |
| `at -q ALPHABET TIMESPEC` | Schedules the job at the queue of the alphabet |
| `atrm JOBNUMBER`          | Deletes job with ID JOBNUMBER                  |
| `at -c JOBNUMBER`         | Views the commands scheduled for the job.      |

***

**Scheduling recurring jobs**

**Scheduling tools**

| Tool      | Use case                       |
| --------- | ------------------------------ |
| `at`      | Scheduling one-time jobs       |
| `Crontab` | Scheduling user recurring jobs |

**Cron Expression**

Scheduling Linux tasks, Scheduling one-time Linux tasks, Scheduling user recurring Linux tasks, Scheduling system recurring tasks and scripts.\
4

&#x20;

text

```
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan, feb, mar, apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun, mon, tue, wed, thu, fri, sat
# |  |  |  |  |
# *  *  *  *  *  <command to be executed>
```

**Examples:**

* `2 * * * *` – At minute 2 of every hour.
* `*/15 * * * *` – Every 15 minutes.
* `0 0 * * 6,0` – At 00:00 on Saturday and Sunday.

***

#### **Crontab**

* Crontab consists of:
  * Cron Expression + command/path
* Crontab can improve system performance.
  * **Examples:**
    * Scheduling reboot.
    * Scheduling running health checks script.
    * Scheduling snapshots.

***

#### **Crontab options**

| Command      | Functionality                             |
| ------------ | ----------------------------------------- |
| `crontab -e` | Opens crontab editor for the current user |
| `crontab -l` | Lists user jobs                           |
| `crontab -u` | Sets the user owner for this job          |
| `crontab -r` | Removes the current cronjob               |

***

#### **Scheduling tools**

Scheduling Linux tasks, Scheduling one-time Linux tasks, Scheduling user recurring Linux tasks, Scheduling system recurring tasks and scripts.

&#x20;

| Tool                 | Use case                         |
| -------------------- | -------------------------------- |
| `at`                 | Scheduling one-time jobs         |
| `Crontab`            | Scheduling user recurring jobs   |
| `/etc/crontab`       | Scheduling system recurring jobs |
| `/etc/cron.d/`       | Directory for system cron jobs   |
| `/etc/cron.hourly/`  | Jobs to run hourly               |
| `/etc/cron.daily/`   | Jobs to run daily                |
| `/etc/cron.weekly/`  | Jobs to run weekly               |
| `/etc/cron.monthly/` | Jobs to run monthly              |

***

#### **/etc/crontab**

text

```
[root@localhost dolphin]# cat /etc/crontab
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan, feb, mar, apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun, mon, tue, wed, thu, fri, sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
```

***

#### **Cron Directories**

Scheduling Linux tasks, Scheduling one-time Linux tasks, Scheduling user recurring Linux tasks, Scheduling system recurring tasks and scripts.

&#x20;

text

```
/etc
├── crontab
├── cron.d/
├── cron.hourly/
├── cron.daily/
├── cron.weekly/
└── cron.monthly/
```

**`cron.hourly/`, `cron.daily/`, `cron.weekly/`, `cron.monthly/`** – Each contains scripts that are executed by `run-parts` according to their schedule.

***

**Anacrontab**

* `anacron` is used for periodic tasks (daily, weekly, monthly) that should be run regardless of whether the system was on or off at the scheduled time.
* It guarantees that tasks will run once the system is back up and the specified period has passed.
* Ideal for systems that are not continuously running, like laptops or desktops.
* `anacron` is a utility that schedules and runs jobs on a system, even if the system is not up at the scheduled time.
* Unlike `cron`, which needs the system to be running at the exact scheduled time.
* It uses the `anacrontab` file (usually located in `/etc/anacrontab`) to define the jobs, which is similar to how cron uses crontab.

**Key Components of `anacron`:**

* **Period:** The frequency at which the job should run (e.g., 1 for daily, 7 for weekly, 30 for monthly).
* **Delay:** How many minutes to wait after the system starts before executing the job.
* **Job Identifier:** A unique name for the job, which helps track whether the job has been executed.

Scheduling Linux tasks, Scheduling one-time Linux tasks, Scheduling user recurring Linux tasks, Scheduling system recurring tasks and scripts.\
7

&#x20;

* **Command:** The actual command or script to be executed.
* `cron`: If the system is off during the scheduled time, the job won’t run.

***

text

```
# /etc/anacrontab: configuration file for anacron

# See anacron(8) and anacrontab(5) for details.

SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22

#period in days  delay in minutes  job-identifier   command
1    5    cron.daily    nice run-parts /etc/cron.daily
7    25    cron.weekly    nice run-parts /etc/cron.weekly
@monthly 45  cron.monthly  nice run-parts /etc/cron.monthly
```

This file is used to schedule tasks on systems where `cron` jobs may not run as reliably, especially on systems that are not always powered on.

**Key Components Explained:**

* **ANACRON:** A utility that allows execution of commands at specified intervals, ensuring that they run even if the system was off during the scheduled time.
* **Random Delay:** Introduces a random delay to prevent multiple jobs from running at the same time.
  * `RANDOM_DELAY=45`: Specifies a maximum random delay of 45 minutes.
* **Start Hours Range:**
  * `START_HOURS_RANGE=3-22`: Indicates that jobs can be started only between 3 AM and 10 PM.

**Job Scheduling:**

* **Daily Jobs:**
  * `cron.daily`: Jobs that need to run daily.
* **Weekly Jobs:**
  * `cron.weekly`: Jobs that should run once a week.

&#x20;

* **Monthly Jobs:**
  * `cron.monthly`: Jobs that are scheduled to run once a month.

If you have any specific questions about this configuration or need further assistance, feel free to ask!

**In this example:**

The configuration file `/etc/anacrontab` might look like this:

text

```
1   5   cron.daily   run-parts /etc/cron.daily
7   10  cron.weekly  run-parts /etc/cron.weekly
30  15  cron.monthly run-parts /etc/cron.monthly
```

* **Daily jobs** (`cron.daily`) will run 5 minutes after the system starts, and they will execute every 1 day.
* **Weekly jobs** (`cron.weekly`) will run 10 minutes after boot, and they will execute every 7 days.
* **Monthly jobs** (`cron.monthly`) will run 15 minutes after boot, and they will execute every 30 days.

<br>
