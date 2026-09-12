# Compressing and Archiving Files and Directories,scp,sftp,rysnc

## **File Compression**

* **gzip:** Faster than bzip2\
  → Used when you want **quick compression speed**.
* **bzip2:** Higher compression ratio\
  → Used when you want to **save as much disk space as possible**.

***

### **Lab: Compressing Files**

1. Create a large file for testing:

```bash
ls -lR / >> myfile
```

2. Compress using gzip:

```bash
gzip myfile
```

3. Decompress gzip file:

```bash
gunzip myfile.gz
```

4. Compress using bzip2:

```bash
bzip2 myfile
```

5. Decompress bzip2 file:

```bash
bunzip2 myfile.bz2
```

***

✅ **Important Note:**\
**Video and audio files use a different type of compression** and usually **do not benefit much** from gzip or bzip2.

***

## **Directory Compression**

* A **directory** only points to one or more files.
* You **cannot compress a directory directly** using gzip or bzip2.
* You must first use the **archive concept**.

***

### **Archive Concept**

* Archiving means **combining multiple files and folders into one single file**.
* Archiving **does NOT reduce file size** by itself.
* It is only a way to **group files together**.

***

### **Lab: Archiving a Directory**

1. Copy `/etc` to your current directory:

```bash
cp -r /etc .
```

2. Show size of all files inside `/etc`:

```bash
du /etc
```

3. Show total size in human-readable format:

```bash
du -sh /etc
```

4. Create an archive using `tar`:

```bash
tar cf etc-backup.tar /etc
```

* `c` → create
* `f` → file (archive file name)

✅ This creates an archive **without compression**.

***

5. Extract the archive:

```bash
tar xf etc-backup.tar
```

* `x` → extract
* `f` → file

***

6. Show files while archiving:

```bash
tar cvf etc-backup.tar /etc
```

* `v` → verbose (shows files during archiving)

***

7. Show files while extracting:

```bash
tar xvf etc-backup.tar
```

***

## **Compression vs Archiving**

* **Archiving:** Combines files into one file (no size reduction).
* **Compression:** Reduces the required disk space.

***

## **Lab: Archiving + Compressing a Directory**

You can use **tar with gzip or bzip2** to both **archive and compress** at the same time.

***

#### ✅ Using gzip:

```bash
tar cvfz etc-backup.tar.gz /etc
```

* `z` → use gzip compression

Extract:

```bash
tar xvfz etc-backup.tar.gz
```

***

#### ✅ Using bzip2:

```bash
tar cvfj etc-backup.tar.bz2 /etc
```

* `j` → use bzip2 compression

Extract:

```bash
tar xvfj etc-backup.tar.bz2
```

***

✅ **Summary Table**

| Task                  | Command                        |
| --------------------- | ------------------------------ |
| Compress file (gzip)  | `gzip file`                    |
| Decompress (gzip)     | `gunzip file.gz`               |
| Compress file (bzip2) | `bzip2 file`                   |
| Decompress (bzip2)    | `bunzip2 file.bz2`             |
| Archive directory     | `tar cf backup.tar dir/`       |
| Archive + gzip        | `tar cvfz backup.tar.gz dir/`  |
| Archive + bzip2       | `tar cvfj backup.tar.bz2 dir/` |

* **Transferring file:scp,sftp,rysnc**
* **Scp**
  *   **Transfers files from a local host to a remote host or vice versa.**

      ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/36b1bbe2-6089-447b-9a24-dba4aaf122a6/e82c4fe2-8f2b-4251-ba5d-0ed1d70d829b/Untitled.png)
* **sftp**
  * **Creates an SFTP session where you can access the remote machine**
  * **create,upload and download content from and to your local machine**
  * `Sftp <host IP / hostname>`
    * `Sftp 192.168.1.1`
    * `Mkdir`
    * `Pwd`
    * `Cd`
    * `Put (upload)`
    * `Get (download)`
* **rsync**
  * **used for backup operations**
  * **Sync between source(local server ) and destination(remote server )**
  * `Rsync –av <source path> <des path>`
  *   `Rsync –av /home/dolphin 192.168.1.1:/home/dolphin`

      **make remote directory like local directory**

      **Note Transfer differnces between two directories on different machines**
