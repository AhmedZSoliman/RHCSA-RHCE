# File management, system commands, and user administration.

### **Managing Files Using the Command Line**

***

#### **1. `mkdir` Command – Create Directories**

Used to create one or more directories.

```bash
mkdir dir1
mkdir dir1 dir2 dir3
```

> If a directory already exists, the system will return an error.

```bash
mkdir /tmp/dir1
```

> Creates `dir1` inside `/tmp` using an **absolute path**.

✅ **Absolute Path:**\
You specify the full location regardless of your current directory.

***

#### **2. `cp` Command – Copy Files and Directories**

Creates an **exact duplicate** of a file or directory.

```bash
cp file1 file2
```

✅ Copies `file1` into `file2` (new name)

```bash
cp file1 /tmp
```

✅ Copy file into another directory

```bash
cp file1 dir/
```

✅ Copy file into a directory

**Copying Directories:**

```bash
cp dir1 dir2
```

✅ Works only if `dir1` is empty\
❌ Gives error if `dir1` has content

```bash
cp -r dir1 dir2
```

✅ Copies directory **with all its content**\
`-r` = recursive

**Example:**

```bash
mkdir /tmp/soliman
mkdir soliman
touch soliman/file.txt
cp -r soliman /tmp
```

✅ Copies the entire directory including its content.

**Copy & Rename:**

```bash
cp dir1 /tmp/dir55
```

✅ Copies directory and renames it

***

#### **3. `mv` Command – Move Files & Directories**

Moves data **without creating a copy**.

```bash
mv dir1 dir2
mv dir1 /tmp
mv /tmp/file.txt /home
```

✅ Can also be used to **rename files**:

```bash
mv file1 file2
```

***

#### **4. `rm` Command – Remove Files & Directories**

```bash
rm file1.txt
rmdir dir1
```

⚠ `rmdir` deletes only **empty directories**

```bash
rm -r dir1
```

✅ Deletes directory with its contents

```bash
rm -rf dir1
```

✅ Force delete (no confirmation)

⚠ **VERY DANGEROUS COMMAND**

```bash
rm -rf dir /
```

If there is a space between `dir` and `/` →\
The system will delete:

* `dir`
* AND the **entire root filesystem `/`**

🔥 **This can destroy the entire operating system.**

***

### **Command Format**

```bash
command [option] [arguments]
```

* **command:** the action
* **option:** changes behavior
* **arguments:** input values
* Anything inside `[ ]` is **optional**
* Spaces between each part are required

#### Examples:

```bash
ls
ls -l
ls -a   # show hidden files
ls -r   # reverse order
ls -R   # recursive display
```

***

### **Keyboard Shortcuts**

| Shortcut              | Function                        |
| --------------------- | ------------------------------- |
| `Ctrl + A`            | Move cursor to beginning        |
| `Ctrl + E`            | Move cursor to end              |
| `Ctrl + K`            | Delete from cursor to end       |
| `Ctrl + U`            | Delete from cursor to beginning |
| `Ctrl + D` or `exit`  | Logout                          |
| `Ctrl + L` or `clear` | Clear screen                    |
| `reset`               | Hard clear without scroll       |
| `Ctrl + Z`            | Pause process                   |
| `bg`                  | Resume paused process           |
| `Ctrl + C`            | Kill process completely         |
| `Ctrl + S`            | Lock screen                     |
| `Ctrl + Q`            | Unlock screen                   |

Run multiple commands:

```bash
ls ; cal
```

***

### **Reboot and Shutdown**

#### **Reboot Methods (All Do the Same Thing):**

```bash
reboot
shutdown -r now
systemctl reboot
init 6
```

#### **Shutdown Methods:**

```bash
shutdown -h now
systemctl poweroff
poweroff
init 0
```

***

### **Adding Users & User IDs**

* When a user is created, the system automatically creates a **primary group with the same name**.
* A user can also belong to **multiple secondary groups**.

#### **Create User:**

```bash
useradd ali
```

✅ Automatically creates a home directory.

#### **Set Password:**

```bash
passwd ali
```

> Without a password, the user **cannot log in**.

* Root can change **any user password**
* Normal users can only change **their own password**

#### **Check User ID:**

```bash
id ali
```

✅ Shows:

* User ID (UID)
* Group ID (GID)
* Group membership
