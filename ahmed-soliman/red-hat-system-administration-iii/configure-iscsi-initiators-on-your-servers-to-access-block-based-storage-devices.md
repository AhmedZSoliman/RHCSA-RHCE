# Configure iSCSI initiators on your servers to access block-based storage devices

## &#x20;

NAS / SAN use external storage.

Why use external storage?\
Because if the data needs, such as for a production database or web server, require a lot of data and also need to provide high availability.

**Types of storage?**

**NAS**

**NAS: stands for Network Attached Storage, and it's like an NFS server.**

**NAS: Network Attached Storage, which means the NAS storage device is located apart (separated) from the hosts that will consume space from it.**

The data traffic between the storage and the host is through the network.

The physical distance between the two may not be noticed; they may be located on the same rack in a data center.

Architecture diagram of a NAS system.

It is quite similar to an NFS server because NAS is a file storage device, unlike SAN (which is a block storage device).

The storage is provided to hosts in the form of a directory, and you export it via NFS to the hosts.

***

iSCSI Server

&#x20;

This type of storage is ideal for centralized shared storage that needs to be accessed at the same time by several different hosts.

This type of storage can be used as backend storage for web servers and application servers and can run databases.

NAS is not recommended storage for installing an OS.

NAS

NAS = Network Attached Storage

NFS SERVER\
□ ......\
□ ......\
□ ......

Network

HOST A

NAS

NFS / CIFS

Reasonably Fast and Reliable

File Based Storage

Shared Storage

Not suitable for OS install

HOST B

SAN: Storage Area Network provides block storage used by companies for business-critical applications.

Storage is allocated to the hosts in the form of a LUN, which stands for Logical Unit Number.

A LUN: is a range of blocks provisioned from a pool of shared storage and presented to the server as a logical disk.

The host system will detect this storage as a raw disk. We can then create partitions and filesystems on top of it, like what we do with any block device, and then mount it on the system to store data.

iSCSI Server

&#x20;

While SAN can also be Ethernet-based, it uses the Fibre Channel protocol. This is a high-speed data transfer protocol that makes use of a Fibre Channel switch to establish communication with the host. The host servers make use of an HBA, or Host Bus Adapter, which is connected to the PCI slot to interface with the Fibre Channel switch. The major advantage of SAN over NAS is that it can be used to host mission-critical applications and databases due to its vastly superior performance and reliability. These include installing Oracle Databases or Microsoft SQL DB, using it for virtualization deployments such as VMware, KVM, Microsoft Hyper-V.

## iSCSI\_Server concept:

\
Any server contains a limited number of disks based on the number of slots, because we will not have scalability.\
Storage solutions: You have a rack with a limited number of slots, so you cannot put a large number of disks.

Storage pool: It means you combine many disks in the rack.\
Shared storage: Big storage; more than one server can access this storage at the same time.\
Storage will be in one place, more than one server can access this storage at the same time, and I don't need servers to have many slots. Maybe I want a server with only two slots.\
This idea will accomplish high availability.\
I don't want to depend on local storage that exists on the server. I want to depend on storage that is not local.

Storage server.\
How to do that?\
We have implementations or groups of protocols that have the ability to do that. Every protocol deals with special links, NICs, and switches except iSCSI.\
iSCSI deals with any normal links, NICs, and switches, and it is used as a protocol to communicate between the client server (iSCSI client) and shared storage (iSCSI server).

iSCSI: Before talking about anything, SCSI stands for Small Computer System Interface, the language that the server uses to communicate with its local hard disks to write data on them.\
SCSI: The language that the servers use to communicate with disks.\
iSCSI: Internet or IP Small Computer System Interface. This language will be used to communicate between iSCSI clients and iSCSI servers (shared storage) over an IP network (Internet).\
iSCSI protocol: Accomplishes the idea that we have a server and this server has attached storage (locally hard disks) and, through the network, it will appear to another server as if it is locally attached.\
iSCSI\_Server&#x20;

* **iSCSI protocol:** Uses TCP connection and the default port is 3260.
* **iSCSI server** has storage called **Target**.
* **iSCSI client** that takes storage from the iSCSI server is called **Initiator**.

***

#### iSCSI\_Server Lab

* **The package name is not `iscsi`; the package name is `targetcli`. Install it on the iSCSI server.**
* **To install the iSCSI server, install this package, and after that, you will have a command line called `targetcli`.**
* **Make sure that the target service is loaded and active.**
  * `systemctl status target.service`
* **Make sure that your backend storage is available (like the disk you added, e.g., `/dev/sdb` on the iSCSI server).**
* **Which is better: to export the disk directly or use it as LVM and after that export it?**
* **One hundred percent LVM, because if the user wants to increase or decrease the storage...**
* **Convert the disk to LVM before exporting it.**
  * `pvcreate /dev/sdb`
  * `vgcreate data /dev/sdb`
  * `lvcreate --size 10G --name oracle data`
* **How to export the storage (one disk or more) through command?**
* **This command has a specific shell.**
* **We can use daily commands like `ls` in this shell.**
  * When you type `ls` inside `targetcli`.

***

iSCSI\_Server

&#x20;

* **`backstores`:** Things that you can export, like block devices, fileio.
  * Block device, fileio: Most of your work will be on them.
  * `cd backstores` after that run `ls` command.
  * `cd block` after that run `ls` command.
* There is no exported block device right now.
  * `cd block` after that run `ls` command.
* Note: You can use the Tab key to complete paths; if you press it in a path, it will show you the available options.
* How to put a block device into the backstore to have the ability to export it?
  * `create sql /dev/data/oracle`
  * `/dev/data/oracle` is the block device I want to export.
  * `sql` is the name given to it.
* `/dev/data/oracle` exists in `backstores` as a block device.
* Now you have exported the block device by the name called `sql`.

\===== Page 7 =====

`/> ls`\
`o- L ...... [...]`\
`o- backstores ...... [...]`\
`| o- block ...... [Storage Objects: 1]`\
`| | o- sql ...... [/dev/data/oracle (10.0GiB) write-thru deactivated]`\
`| | o- alua ...... [ALUA Groups: 1]`\
`| | o- default_tg_pt_gp ...... [ALUA state: Active/optimized]`\
`| o- fileio ...... [Storage Objects: 0]`\
`| o- pscsi ...... [Storage Objects: 0]`\
`| o- ramdisk ...... [Storage Objects: 0]`\
`o- iscsi ...... [Targets: 0]`\
`o- loopback ...... [Targets: 0]`\
`o- vhost ...... [Targets: 0]`

Note `cd` command.\
Use `cd` to navigate to the root or any other location.

Go inside iSCSI: `cd iscsi`.\
Press Tab twice after `create`; you will find `create wwn=`

What is WWN?\
iSCSI server doesn't communicate with the iSCSI client by hostname.\
They don't use the hostname that we use in most of the services.\
When talking, the iSCSI server will have a special name, and the same for the iSCSI client.\
They will use IQN: stands for iSCSI Qualified Name. It's like the hostname they use to communicate with each other (iSCSI server and iSCSI client).\
IQN: is a unique name for your device.\
Syntax: `iqn.date.reverse_domain_name.server_name`\
`iqn.date`: Write the year and month. Usually, it starts with the year then the month.\
Example: `iqn.2013-05`. By the way, you can change it to any date.\
`reverse_domain_name`: The domain name reversed.\
`server_name`: The server name.

&#x20;

* Example: Imagine you have a server with the hostname `storage.mostafa.com`.
  * **The IQN will be `iqn.2024-01.com.mostafa.storage`.**
* **Make an IQN on the iSCSI server.**\
  `/iscsi> create iqn.2024-01.com.ahmed.server`
* Note: In a production environment, you would use the actual date, etc.
* **Notes:**\
  When you create an IQN, automatically a portal is added ---\
  **`0.0.0.0:3260`**.\
  **iSCSI server can connect with more than one interface at the same time.**\
  **Storage servers have many interfaces.**\
  **Each interface will connect with a switch; teaming will not happen here because each switch is standalone.**\
  You could say we will make teaming on the same switch, but if that switch goes down...\
  For example, you are not in a stable environment, think about it.\
  **You know that the iSCSI server connects on some interfaces while it listens on other interfaces.**

&#x20;

* **The interfaces that listen serve iSCSI traffic; they are called portals.**
* **Portals:** are the interface's IP that listens to iSCSI traffic (incoming and outgoing traffic).
* **Portal group:** IP + port.
  * When you make an IQN, it automatically makes a default portal. They listen on all interfaces (each interface has an IP) by default.
    * Created default portal listening on all IPs (`0.0.0.0`), port 3260.
    * Created TPG 1, stands for Target Portal Group 1.
* **ACL (Access Control List): Does the server accept any connection from any client? No.**\
  **If the client wants to access the server, it should have permission to do that.**\
  **How does the iSCSI client use or connect to the iSCSI server? Not by IP, not by hostname, but by IQN.**
* **Go inside ACL.**\
  `cd /iscsi/iqn.2024-01.com.ahmed.server/tpg1/acls`\
  Come here to configure the client that is allowed to communicate with the server.\
  In ACL, we will create an IQN for the client, and remember we will use the same name on the iSCSI client.\
  `create iqn.2024-01.com.ahmed.client`\
  `ls`
* `/dev/data/oracle` is the block device that will be exported by the name `sql`, and it will be exported via the interfaces `0.0.0.0:3260`, and there is an IQN for the server and another IQN for the client. ---> We are preparing to export it.

&#x20;

* **Have we exported the block device yet? No. If you want to export it, it will be via LUNs.**
* **The shared block device that the server has and wants to export. The client sees it as a LUN, so you will need to export the block device as a LUN.**
* **Go inside the LUNs.**\
  `cd /iscsi/iqn.2024-01.com.ahmed.server/tpg1/luns`
* **Create a LUN.**\
  **It does a map for this node, and in other words, now we export the block device on the iSCSI server.**\
  **Now we export the block device on the iSCSI server.**
* **Note:** When you export a block device, you export it by LUN.

&#x20;

* For sure we need security. So when the client wants to take the LUN, it must enter a username and password.
  * Go to this directory:\
    `/iscsi/iqn.2024-01.com.ahmed.server/tpg1/acls/iqn.2024-01.com.ahmed.client`
  * Set the username:\
    `set auth userid=user_name`
  * Set the password:\
    `set auth password=password`
* **To exit from targetcli:**\
  `exit`
* When you exit from targetcli, it automatically saves the configuration in this directory in JSON format:\
  `less /etc/target/saveconfig.json`
* **Execute this command to know the status of the iSCSI server:**\
  `systemctl status target.service`
* **How to know that the service listens on port 3260:**\
  `netstat -ntlp | grep -i 3260`
* **To know the status of the firewall:**\
  `systemctl status firewalld`
* **You have to allow this port 3260 on the firewall:**\
  `firewall-cmd --add-port=3260/tcp --permanent`
* That's it; we have finished with the server. Let's go to the client.
* **Client:**
  * On the client, install the iSCSI client package:\
    `yum install iscsi-initiator-utils`\
    Remember, the iSCSI client is called the initiator.
  * You need to modify the initiator name in this directory.

&#x20;

`vim /etc/iscsi/initiatorname.iscsi`

Inside it:\
`InitiatorName=iqn.2024-01.com.ahmed.client`

* **Remember: This is the initiator name that is identified on the server: `iqn.2024-01.com.ahmed.client`.**
* If you want to communicate (send packets) with the server, use this command `iscsiadm`:\
  `iscsiadm --mode discoverydb --type sendtargets --portal 192.168.43.19 --discover`\
  **To ensure the connectivity between the client and the server.**\
  Output example: `192.168.32.157:3260,1 iqn.2024-01.com.ahmed.server`
* **Enable the iSCSI service on the client:**\
  `systemctl enable iscsi.service`\
  `systemctl start iscsi`
* **Display the status of the iSCSI service:**\
  `systemctl status iscsi`\
  **If you find it inactive, run this command:**\
  `systemctl --system daemon-reload`
* **Remember we use authentication (username and password) on the server for the client that is allowed to access the LUN.**
* Modify inside this configuration on the iSCSI client:\
  `vim /etc/iscsi/iscsid.conf`
* **Search for the word called `CHAP` and uncomment this line:**\
  `node.session.auth.authmethod = CHAP`
* **And also uncomment these two lines:**\
  `node.session.auth.username = username`\
  `node.session.auth.password = password`

&#x20;

* **Note:** When you scan (communicate with the iSCSI server) the target, automatically on the client, a target's directory is created containing the config about the target server.\
  `[root@client ~]# less /var/lib/iscsi/send_targets/192.168.32.157,3260/st_config`
* **When the iSCSI client logs in, it catches the storage on the iSCSI server.**\
  `[root@client ~]# iscsiadm --mode node -T iqn.2024-01.com.ahmed.server -p 192.168.32.157 --login`
* Type `lsblk` or `fdisk -l`, and you will find new storage added to your device.
* When you reboot the client, you will find that the block device (on the iSCSI server) is attached automatically by the iSCSI client.\
  `reboot`
* On the client, you will find Nodes; inside the Nodes, you will find the iSCSI server's node.\
  `[root@client ~]# less /var/lib/iscsi/nodes/iqn.2024-01.com.ahmed.server/192.168.32.157,3260,1/default`
* `node.startup = automatic` This is an option inside the file. It means the shared storage will be attached automatically as long as the server exists and is available, even if you reboot.
* This file contains info about the server.
* The storage that is added to your machine: you have the ability to make formatting and mounting for this storage.
* iSCSI server and client configuration takes at least 5 to 10 minutes from you.
* **Server:**\
  I want to share a block device, and its backend is a file.\
  `dd if=/dev/zero of=/root/bigfile bs=1M count=100`\
  `targetcli`

&#x20;

* `cd backstores/fileio`
* `create myfile /root/bigfile`
* `cd /iscsi/iqn.2018-07.com.iscsi.server/tpg1/luns`
* `create /backstores/fileio/myfile`

**Client:**\
Is a new device added or not? If not, run this command because you have to logout first:\
`iscsiadm --mode node -T iqn.2018-07.com.iscsi.server -p 192.168.43.19 --logout`

After that, log in, and you will find that the device is added:\
`iscsiadm --mode node -T iqn.2018-07.com.iscsi.server -p 192.168.43.19 --login`

`lsblk`\
The new disk appears, although it is essentially based on a file. You can discuss that it is a block device originating from a file. You can make a filesystem and mount this file.

Option delete: To remove the configuration from the client side.\
`iscsiadm --mode node -T iqn.2018-07.com.iscsi.server -p 192.168.43.19 -o delete`

<br>
