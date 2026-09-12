# Managing users, groups, permissions, and ownership.

## **Basic User & Group Administration**

* **The normal user can only modify its home directory.**
* When you create a user, its info is stored under:

```bash
cat /etc/passwd
```

* Users' passwords are stored under:

```bash
cat /etc/shadow
```

***

### **Group Management**

* **`groupadd` command**

```bash
groupadd dcadmins
groupadd networkadmins
groupadd dbadmins
```

* When you create a group, its info is stored under:

```bash
cat /etc/group
```

* Group passwords are under:

```bash
/etc/gshadow
```

* The group info format: `group name : hidden password : group admin : list of users belonging to this group`
* Private groups (primary groups) are automatically created when you create a user.

***

### **User Creation & Group Assignment**

* Create a user and assign it to a group:

```bash
useradd -g dcadmin omar
```

* `-g` changes the primary group of the user.
* Check groups:

```bash
cat /etc/group
```

***

#### **Modify User Groups**

* Add a secondary group:

```bash
usermod -G networkadmins mostafa
```

* Overwrite secondary group:

```bash
usermod -G dcadmins mostafa
```

* Add more than one secondary group:

```bash
usermod -a -G networkadmins Mostafa
```

* **Note:** When you create a user, the system creates a home directory under `/home`.

***

### **Deleting Users & Groups**

* Delete a user (files remain):

```bash
userdel ahmed
```

* Delete a user and its files:

```bash
userdel -r ahmed
```

* Delete a group:

```bash
groupdel dcadmins
```

* **Primary group importance:** File ownership is identified by the user and their primary group. Removing a user does not affect other users in the same group.
* If adding a user fails due to a missing group, specify the group explicitly:

```bash
useradd -g ali ali
```

***

#### **Summary Commands**

| Command                    | Purpose                                            |
| -------------------------- | -------------------------------------------------- |
| `groupadd user`            | Add group on the system (`/etc/group` stores info) |
| `/etc/gshadow`             | Group passwords                                    |
| `useradd user`             | Create user on the system (`/etc/passwd`)          |
| `/etc/shadow`              | Users’ passwords                                   |
| `passwd user`              | Create password for user                           |
| `useradd -g group user`    | Add primary group when creating user               |
| `usermod -g group user`    | Change primary group                               |
| `usermod -G group user`    | Add secondary group                                |
| `usermod -a -G group user` | Add additional secondary groups                    |
| `userdel user`             | Delete user (files remain)                         |
| `userdel -r user`          | Delete user with files                             |
| `groupdel group`           | Delete a group                                     |

***

## **Basic Permissions**

* `ls -l` → shows files, file owner, group owner, and permissions
* Any file is handled by **three entities**:
  1. **Owner/User:** The user who owns the file
  2. **Group:** Users in the same group as the owner
  3. **Others:** Everyone else
* Operations possible:
  * **Read:** View file content
  * **Write:** Modify, delete, or overwrite file
  * **Execute:** Run binary or shell script

***

### **File Types Symbols**

* `-` → Normal file
* `d` → Directory
* `b` → Block device (e.g., hard disk)
* `c` → Character device (e.g., keyboard)
* `l` → Symbolic link (shortcut)
* Example:

```bash
ls -l file2
```

* Output explained:
  * **File type:** `-` normal file
  * **User permissions:** `u(rw)`
  * **Group permissions:** `g(r)`
  * **Other permissions:** `o(r)`
  * **Link count:** `1`
  * **Owner:** root
  * **Group owner:** root
  * **Last access time**
  * **File/directory name**

***

### **Changing Permissions (`chmod`)**

* `chmod` changes file permissions.

**Symbolic (letters)**

```bash
chmod o+w file2      # Add write for others
chmod o-rw file2     # Remove read/write for others
chmod g-rwx file     # Remove all for group
chmod ugo+rwx file   # Add all permissions for everyone
chmod ugo-rwx file   # Remove all permissions
chmod ug+r,go-wx file2
chmod u=rw,g=rw,o=x file
```

**Numeric (digits)**

* Assign digits to permissions: `r=4`, `w=2`, `x=1`
* Example:

```bash
chmod 755 file1  # u+rwx, go=rx
```

* Directory recursive application:

```bash
chmod -R ug-rw dir1
```

**Permissions meaning**

| Type      | Read                  | Write                 | Execute           |
| --------- | --------------------- | --------------------- | ----------------- |
| File      | view content          | edit/delete           | run binary/script |
| Directory | `ls` to view contents | add/edit/delete files | `cd` to access    |

***

## **Ownership (`chown`)**

* Change ownership of file or directory:

```bash
chown ali work/         # Change user ownership
chown :dcadmins work/   # Change group ownership
chown ali:admins work/  # Change user and group ownership
```

* **Note:** Only the owner can change file permissions.
