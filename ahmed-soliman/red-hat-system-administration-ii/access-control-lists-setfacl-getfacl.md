# Access Control Lists (setfacl, getfacl)

Access Control

### Lists(setfacl,getfacl)

#### Access Control Lists

* **I have some files that belong to specific users and I want to be able to give other users permissions on the same file**
* **useradd name\_user to add user**
* **passwd name\_user to change the user password**
* **In life, everyone always has a main job, and bringing the job is a secondary matter, similar to the group that owns the file group.**
  * **user1: owner of the file, user1:primary group.**
  * **One user, one group.**

***

\[user1@client1 \~]$ ls -1 file1\
-rw-rw-r--. 1 user1 user1 0 Mar 11 23:10 file1

* **groupadd group\_name**
* **usermod-g group\_name username to change the primary group of the user**
* **id username display primary group**

***

Access Control Lists(setfacl,getfacl)

&#x20;

* secondary group Each one only, but a user can be a member of more than one.\
  A user has:
  * usermod -G group\_name user\_name to change the primary group of the user
  * you can have more than secondary group but only one the primary group.
  * usermod -G -a group\_name user\_name -a to append to list of secondary groups not overwrite.

To give permissions on a file to a specific user who does not have permissions on it, you will resort to the\
access control list

* most of the kernels support access control list (ACL)
  * /boot is the place where the kernel exists
  * How to know if the kernel supports ACL or not\
    con...: this file contains the options that the kernel supports

| **root@ahmed** | **\~]# cat /boot/config-6.2.9-300.fc38.x86\_64** | **grep ACL** |
| -------------- | ------------------------------------------------ | ------------ |

* To use the access control list, check your file system
  * Because if the file system is xfs, it means that automatically this file system supports access control list
  * During mounting normal file system like ext4, ext3, tell it to support access control list
  * fdisk /dev/sda to create partitions for disks
* To make file system

\| **root@client1** | **\~]# mkfs.ext4 /dev/sdb1** |

* Because check the type of file system in this case we consider it ex: ext4
* During mounting the partition has normal file system, we support ACL

\| **root@client1** | **\~]# mount -o acl /dev/sdb1 /srv/** |

Access Control Lists(selfacl, getfacl)

&#x20;

* If you mount partition has normal file system, and you forget to support ACL\
  Do remount and add option ACL for the partition has normal file system that mounted at this location

\[root@client] \~]# mount -o remount.acl /srv/

* vim /stab don't forget this one
* However, note that in new distributions, developers enable ACL support in the file system by default

How to apply the access control list

setfacl-m u:user2:rw file\_name -m modify :-u user:permission

* getfacl file\_name get the access control list about the file or directory
* setfacl-R -m g:user2:rwx /directory\
  Give here a specific group permissions on a folder
* R recursive g group : group\_name: permissions directory\
  Without -R it will be only on the directory from outside

ex:

\[user1@client1\_\~]$ getfacl ./work/

group:user2:rwx

This means any user who is a member of this group will have these permissions

* Note that you applied ACL on a folder with all its contents for a specific user, so all files will have permissions on them, and this is according to the permissions you set, but it will be on the files existing in the folder at the current time. Therefore, if a new file is created in the directory, it will not have permissions on it, and the solution is to make it so that when you create a file automatically you have permissions on it
* setfacl -R -m d:g:user2:rwx /work d → default permission\
  This will be for files added from the moment you enable monitoring

Access Control Lists(setfacl.getfacl)

&#x20;&#x20;

without default permission\
it applies to the files currently in the directory, but any new file does not

* -x means delete the permissions for this user

\[user1@client] \~]$ getfacl --> < uruser>/work/

* -b: used to remove the permissions on file or directory

\[user1@client] \~]$ setfacl -b /work/

\[user1@client] \~]$ getfacl ./work/

&#x20;
