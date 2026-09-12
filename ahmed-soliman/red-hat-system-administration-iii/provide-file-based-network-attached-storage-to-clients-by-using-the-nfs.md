# Provide file-based network-attached storage to clients by using the NFS

## NFS\_Server

* **NFS: stands for Network File System.**
* This service is used to share files between Linux and Unix.
* Mostly, there is no production environment that supports NFS on Windows Server.
* NFS is used a lot in data centers.
* If you use VMware, the deployment of NFS is extensive. Needless to say, VMs are hosted on specific storage, and files are shared through NFS, iSCSI.
* **NFS problem:** is High Availability. Because when clients connect to the server for reading and writing data, if the server goes down, the connection is also down.
  * To solve this problem, they use implementations like Parallel NFS (pNFS).
* On the server, install the NFS package.\
  **The package name is `nfs-utils`.**\
  We use the same package on the server and client. NFS is supported by the kernel.\
  **Install the NFS package.**
* **Which port does NFS listen on? NFS doesn't have a dedicated port.**\
  It doesn't have a specific port it works on. You see a free port and it runs on it. So, when the client comes to deal with this service, the client doesn't know a specific port.
* **NFS depends on a service called portmap.**
  * **Portmap:** is a service that finds a free port, reserves this port for the NFS service, and runs the service on it.\
    What about this randomness? How does the client know the port this service is running on?

&#x20;

* To know this, when the client communicates with the server, both the client and server have **rpcbind**.
* **RPCbind (Remote Procedure Call):** First, the client sends an RPC request to the server. In this request, the client asks the server which port the NFS service is working on, and then the client tries to connect to that port.
* The real problem is that the ports reserved for this service are random, and it's very difficult to configure the firewall to allow this service because firewall rules are static. As a system administrator, you have to identify a static port for NFS because of the firewall.
* **On the server.** First of all, the configuration of NFS is one of the easiest configurations you have ever seen in your life: `vim /etc/exports`.
* On the server, by default, this configuration file is empty because, by default, the server exports nothing.\
  `vim /etc/exports`
* On the server, I want to export or share a file or directory, and the clients can access this directory or file for reading or writing.\
  Example: `mkdir /data` I want to share this directory with permissions, for example, read-write (rw) or read-only (ro).
* On the server and the client, you have to enable and start the service.\
  `systemctl status nfs.service`\
  `systemctl enable nfs.service`\
  `systemctl start nfs.service`
* On the client, sometimes the client doesn't see the exported or shared directory, so you have to restart the service.\
  `systemctl restart nfs`
* If you want to see the port that NFS is working on:\
  `netstat -ntlp`\
  Example output: `tcp6 0 0 :::2049 :::* LISTEN`

&#x20;

&#x20;

And rpc is running as well, with some related services.

* On the server, make a directory: `mkdir /data`.
* On the server, go to the NFS config: `vim /etc/exports`.
* On the server, inside this file `/etc/exports`, put the directory you want to share, and you must specify who you are sharing it with.
  * `/data *(rw)` : Share this directory, and `*` means for all networks with read and write permission.
  * If you want to share the directory or file to a specific network, write its subnet inside the config like this: `/data 192.168.0.0/16`.
  * Instead of an asterisk, you can put an IP address, domain name, or subnet.
* On the server, try to share with everyone, so write in the export file: `/data *(rw)`.
* On the server, now you have shared or exported this directory. After that, if someone tells you to restart the service, restarting the service is a bad decision because if you add a new directory and clients are accessing the server, they will lose the connection.
  * Run this command: `exportfs -r`. This command will see what has been added to the NFS configuration file.
* On the NFS client, try to communicate with the NFS server to gain access to directories and files.\
  `showmount -e server_ip`\
  **Error example: `RPC: Unable to receive`.**
* Here, it cannot communicate with the service. This means the firewall is blocking this service.
* On the server, make the firewall allow those services.
  * `firewall-cmd --add-service=nfs --permanent`
* If you go back to the client after a step like this, it's not enough. You will still face the same error. Therefore, you must also allow the `rpc-bind` service on the firewall, which is the service that knows the port it is working on.
  * `firewall-cmd --add-service=rpc-bind --permanent`

&#x20;

You might still get an error message on the client when trying to access.

* Try to stop the firewall: `systemctl stop firewalld` and test on the NFS client. Needless to say, I saw the shared directory. Don't do that in a production environment.
* `systemctl start firewalld`
* Add the service called `mountd` on the firewall.\
  `firewall-cmd --add-service=mountd --permanent`\
  `firewall-cmd --reload` You have to reload the firewall.
* Do a restart to NFS if there is a change: `systemctl restart nfs`
* On the client: `showmount -e server_ip`. The client will now have the ability to see exported or shared directories because you added the service called `mountd`. Remember, rpc is not one service; the client communicates with the server through this service to know the port that NFS is working on.
* On the server, we need to enable `rpcbind` to allow the client to discover the server, and the client has the ability to mount the directories.
* **Note:** NFS allocates dynamic ports based on portmap and needs more than one service to work.\
  `systemctl status nfs` (Press Tab twice to see all the services.)
* On the client, mount the shared directory:\
  `mount -t nfs server_ip:/data /opt/`\
  `df -h` to see the mount.
* **Note:** The server shares the directory, and the client mounts this directory without any problems. If the client writes files, who is the owner of these files?\
  Example: `touch file1 /opt` (The mounted location).\
  Error: `touch: setting times of "/opt": Permission denied`
* Why can't you write despite sharing with `rw`? Because of SELinux. Stop SELinux: `setenforce 0`. Despite stopping SELinux, you still can't write. By the way, the firewall has nothing to do with this topic anymore.

&#x20;

&#x20;

* On the client, unmount: `umount /data`.
* **On the server**, why can't the client write inside the directory? Because of the permissions. The directory needs write permission for others: `chmod o+w /data`.
* On the client, mount the directory again: `mount /data`.
* On the client, now you can create a file or directory inside this location without any problems.
* **On the client:**\
  `[root@client srv]# ls -l`\
  `total 0`\
  `-rw-r--r--. 1 nobody nobody 0 Jan 4 18:14 file1`
* **Who is nobody?**
* **Sir, we have two problems.**
  * **The first one:** The person who shared the directory on the server is a normal user or root. Needless to say, he is root. Also, the user who mounted the directory on the client is root.
  * **Is the root on the server equal to the root on the client? NO.** Normally, they are different.
  * Do you think the server will share the directories with root permissions or that the owner is root? And if the user on the client is root, he will do anything he wants inside the shared directory? Actually, he will make a mess and do mischief.
  * To solve this problem, the NFS server maps (changes) the root user on the client device to an NFS user called `nobody` (it means a user without any permissions).
* **On the server,** if you share a directory and you fully trust someone who wants to have full control, meaning the client has full control, I don't want the NFS server to map the user on the client device to `nobody`.
  * Go to the config file `vim /etc/exports` and add this word: `no_root_squash`.

&#x20;

Now, on the client, if you go and create a file, you will find the owner is root.\
Note: It does not change the root to a user with no permissions to prevent the root on the client from having the same permissions as the root on the server, so he might manipulate things.

* On the server, start SELinux again: `setenforce 1`.
* On the client, to automatically mount the directory, put it in `fstab`: `vim /etc/fstab`\
  `192.168.32.157:/data /srv nfs4 defaults 0 0`
* Suppose you reboot the client machine, and it mounts from the file during boot. Suppose the server is down for some reason, and the client will show you a message like this:\
  `A start job is running for ... (25s / 1min 38s)`
* There is a trick you can do in the file. Change the option from `defaults` to `defaults,_netdev`.\
  `192.168.32.157:/data /srv nfs4 defaults,_netdev 0 0`
* `_netdev` (Network device is a device accessible through the network): You can put it when you mount any filesystem to tell the client that the filesystem is a network filesystem or this filesystem is accessible through the network.
* Don't do a mount via `fstab` without this if it's a network filesystem.
* Any shared filesystem like NFS, iSCSI, Samba, if you want to mount those shared storages in `fstab` for automount when you reboot the machine, you have to add this option `_netdev` in `fstab`, and now you know why.

&#x20;
