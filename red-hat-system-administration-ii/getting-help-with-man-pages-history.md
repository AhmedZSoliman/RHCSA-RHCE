# Getting help with man pages,History

## How to Help Yourself by Yourself

Do you think a normal person will remember commands with all their options?\
Nobody does that, only a crazy person.

Anyone who tells you to memorize every command with its options, take a gun and shoot him twice and relax.

You know the command but you forget the options,\
or you even forget the command name completely.

There are at least **five or six ways to help yourself**.

***

### 1- Man Pages → Manual Pages (User Guide)

#### ✅ Syntax of Any Command:

```
command [option] [argument]
```

* `#` at the beginning means you run the command as **root**
* `$` at the beginning means you run the command as a **normal user**
* By the way, these two signs are **not written in the terminal**
* `[ ]` anything between brackets is **optional**
* **Option**: changes the behavior of the command

***

#### ✅ Examples:

```
man ls
man nmcli
```

`man` is not only for commands,\
it is also for **configuration files and admin commands**.

***

#### ✅ Man is Divided into Sections (Chapters)

* **Chapter 1** → user commands
* **Chapter 5** → configuration files
* **Chapter 8** → administration commands

***

#### ✅ Examples:

```
man 5 ls
man 5 passwd   → tells us about the configuration file
```

```
man man
```

Don’t try to memorize anything, you will see all the chapters and everything you need.

By the way, **man gives you examples about commands**.

The trainer says he never memorized a command,\
you may know what the command does but you don’t know its name.

***

#### ✅ Search Inside Man:

```
man -k date
```

Means search for the command that shows the date.

***

#### ✅ Man Page Navigation:

* `g` → go to the beginning
* `G` → go to the end
* `/Keyword` → search like vim
* `n` → next result
* `N` → previous result
* `q` → quit the man page

***

### 2- Info Pages

Info pages are like man pages but have **more information and a lot of details**.

Example:

```
info nmcli
```

But in some commands you will need more explanation, so go to **man**,\
**most commands need man**.

***

### 3- wh Commands

#### ✅ whatis

```
whatis command_name
```

Gives you a **simple explanation** about the command.

***

#### ✅ whereis

```
whereis command_name
```

Tells you:

* the **command binary location**
* and also the **man pages organize these to sections**

## 4- `/usr/share/doc` (Documentation for Installed Packages)

* Most packages, when you install them on the system, create their **documentation inside this directory**:

```
/usr/share/doc
```

* When you configure **any service**, this place is considered the **spiritual father (main reference)** for configuration.
* Example:\
  When you install a **DHCP server**, you take a **sample configuration** from this location:

```
/usr/share/doc
```

* When you start configuration:
  * You **copy & paste**
  * Then you **edit**
  * You **do NOT write everything from scratch**

***

## 5- Search on Google (RedHat Documentation)

* You can search on Google for:

```
RedHat Documentation
```

* This is one of the **best official sources** for learning and solving problems.

***

## 6- Community (Websites & Forums)

* Websites on the internet and forums can help you a lot.

#### ✅ Example Websites:

* `cyberciti`
* `howtoforge`
* `thegeekstuff`
* Forums are **one of the best places to ask questions** and get help.

#### ✅ Example Forums:

* `Fedoraforums`
* `linuxquestions`

***

## History

* The `history` command is **used to view previously executed commands**.
* It is stored **separately for each user**.

***

#### ✅ Useful History Commands

* Execute the last command:

```
!!
```

* Execute a command by its number:

```
!command_number
```

Example:

```
!1024
```

* Execute the 4th command from the bottom:

```
!-4
```

***

#### ✅ Where History is Stored

* The system stores previous commands in this file:

```
~/.bash_history
```

* This file exists in the **home directory of each user**.
* No one can see another user’s history\
  ❗ Except **root**, because it is a special case.

***

#### ✅ Important Note About RAM & History

* History is first written in **RAM (volatile memory)**.
* It is saved into `.bash_history` only when:
  * You **logout**
  * Or do a **normal shutdown**
* If electricity goes out suddenly ❌\
  The kernel **will NOT save** what is in the memory to the disk.

***

#### ✅ Clear History from RAM

* To clear history from memory:

```
history -c
```

* After that:

```
cat .bash_history
```

* You will find:
  * History removed from **RAM**
  * But **not removed from disk**
  * It will be removed from disk after **logout**

***

#### ✅ Another Way to Remove History

* This way is also used to remove history:

> Remember:

```
>   overwrite
>>  append
```

***

#### ✅ History Size

* You can **increase the size of history**.
* You are free to make it bigger.
* Nobody will say anything.

***

## Auto Complete

* Auto-complete is used for:
  * Commands
  * Paths
* To use it:

```
Press Tab
```

✅ It makes your work **much faster** and **easier**.

***

