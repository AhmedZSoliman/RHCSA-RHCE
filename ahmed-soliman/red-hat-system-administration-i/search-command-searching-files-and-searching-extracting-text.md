# Search Command ,Searching Files  and Searching/Extracting Text

## **Search Commands (whatis, whereis)**

* `whatis ls`\
  → Shows a short description of the command.
* `whereis ls`\
  → Shows where the binary file is and also its documentation.

***

## **Search Files (locate, find)**

### **locate**

* `locate file_name`\
  → This command searches for files using a **database (locate DB)**.
* The system tracks:
  * Files added to the partition
  * Files removed from the partition
* The database is updated **at a specific time each day**, not all the time.
* Example:
  * `locate network`
* `updatedb`\
  → Updates the locate database **now**.

***

### **find**

* `find file_name`\
  → Used to search in the filesystem or partition in **real time**.
* It does **not use a database**.\
  It scans the partition directly to find the file.
* You can search for many things like:
  * File name
  * File owner
  * File permissions

***

#### **Examples of find**

* `find /etc/ -name network`\
  → Search for files with the exact name **network**.
* `find /etc/ -iname network`\
  → `-iname` means **case insensitive** (capital or small letters do not matter).
* `find /etc/ -iname network*`\
  → Search for anything that **starts with network** and ends with anything.
* `find -iname network`\
  → Search in the **current directory** only.
* `find /var -perm 755`\
  → `-perm` means **permissions**.
* `find /home -group ahmed`\
  → `-group` means **group owner**.
* `find /home -user ahmed`\
  → `-user` means **file owner**.
* `find /home -atime 1+`\
  → Files accessed **24 hours ago or more**.
* `find /home -atime 1`\
  → Files accessed in the **last 24 hours**.
* `find -inum 2012352`\
  → Find the file using the **inode number**.

***

#### **Save Search Results to a Directory**

* If you want to save search results to a specific directory:

```
find /etc/ -iname network -exec cp -r {} dirName/ \;
```

* `-exec` → Execute a command
* `{}` → Takes the result from the find command
* `cp -r` → Used for copying directories

***

## **Searching Inside Files (grep)**

* `grep word file_location`\
  → Searches **inside the file content**,\
  **not the file name**.

***

#### **Examples of grep**

* `grep root /etc/passwd`\
  → Search for the word **root**.
* `grep -i root /etc/passwd`\
  → `-i` means **case insensitive**.
* `grep -v root /etc/passwd`\
  → `-v` shows lines that **do not contain root**.
* `grep -iv root /etc/passwd`\
  → Case insensitive + exclude root.
* `grep -n root /etc/passwd`\
  → `-n` shows the **line number**.
* `grep -R root /etc`\
  → Recursive search inside directories.
* `grep -ilR root /etc`\
  → Show **only file names**.

***

## **cut Command**

* `cut -f 1 -d : /etc/passwd`
  * `-f` → Field (column)
  * `-d :` → The separator between columns
* `cut -f 1,3 -d : /etc/passwd`
* `cut -f 1-3 -d : /etc/passwd`
* `cut -f 1-3 -d : /etc/passwd | sort -n`
  * `-n` means **numeric sorting**
* `cut -f 1,7 -d : /etc/passwd`
* `cut -f 7 -d : /etc/passwd`\
  → If you want the result **only once**:
  * `cut -f 7 -d : /etc/passwd | uniq`\
    → Compares each result with the one before it
  * `cut -f 7 -d : /etc/passwd | sort | uniq`
