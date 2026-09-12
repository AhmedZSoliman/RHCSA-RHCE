# Configure FTP  Server and Client

## FTP\_Server

* You must install the service and ensure that the service is up and running. Also, ensure that the firewall allows this service and that it is accessible through the internet.
* **On the server**, install the FTP service package called `vsftpd` → stands for Very Secure FTP Daemon.\
  `yum install vsftpd`
* **On the client**, install the FTP package.\
  `yum install ftp`
* **On the server**, check the status of the `vsftpd` service. Is it working or not?\
  `systemctl status vsftpd`
* **On the server**, if it is not working, you must start it.\
  `systemctl enable vsftpd`\
  `systemctl start vsftpd`
* **On the server**, make sure that the firewall allows this service.
* **On the server**, how to know the allowable services and ports.\
  `firewall-cmd --list-all`
* **On the server**, make the firewall allow this service.\
  `firewall-cmd --add-service=ftp --permanent`
* **On the server**, reload the firewall configuration.\
  `firewall-cmd --reload`
* **FTP Server**: Allows hosting files, and users connect to the server to download these files.
* **FTP works on two ports:**
  * Port 20: Control traffic.
  * Port 21: Data transfer.
* **FTP uses a TCP connection.**

&#x20;

Why do you need FTP?\
When you want to share files with users in the company, someone might say, "But we can share files through HTTP?" You are right, but FTP will give you options not available in HTTP. For example, FTP allows uploading files from the client machine, and each user can access their home directory remotely.

* **On the client**, test the connection between the FTP client and the server.\
  `ftp server_ip`
* **On the client**, the line meaning: Log in to the FTP server.\
  `root:` It means when you connect to the FTP server, you use the root user. We can use it for the authentication process.
* **On the client**, if you do not configure any authentication between the client and the server, you can log in as an anonymous user (the login will be successful, of course).
* **On the server**, the FTP server serves files from this location `/var/ftp/pub`. Any file you place on this path will be seen by the users.
* **On the client**, how can we download files locally to your machine?\
  `get file_name`
* **On the server**, as a system administrator, there are some things you can do with this service. The configuration file for this service is:\
  `vim /etc/vsftpd/vsftpd.conf`\
  FTP\_Server\
  2

&#x20;

* **On the server**, the configuration file for this service.
  * Inside this file, there are parameters you can modify. For example, you can comment out anonymous login or set the value of anonymous login to NO.
  * **On the server**, this configuration file.
  * All options follow the format `VARIABLE=VALUE`.\
    `VALUE` is in uppercase.\
    `variable` is in lowercase.
* **On the server, in the configuration file:**\
  `# Allow anonymous FTP? (Beware – allowed by default if you comment this out).`\
  `anonymous_enable=YES`
  * If you comment this line, it does not disable it because it is enabled by default. If you want to disable it, enter `NO`.
* **On the server, we will work with options in the configuration file.**
  * `local_enable=YES` or `NO`
    * It means the users that you create or have on the server have the ability to access the FTP server.
    * Allows local users to log in.
    * If you log in as a local user (from the server users), you are redirected to your home directory on the server, not this directory `/var/ftp/pub`.
    * Home directory: It is the place you see from the client and can interact with through the FTP server.
    * But actually, on the server, you are standing in your home directory. This means you can upload a file from the client to your home directory on the server.

&#x20;

* `write_enable=YES` allows you to upload files.
* `local_umask` specifies the default file permission mask for files uploaded by FTP clients. It is used to control the permissions of files created or modified through FTP.
* `#anon_upload_enable=YES` This option will allow anonymous users to upload files.
* `#anon_mkdir_write_enable=YES` Allow users to create directories on the server.
* All are self-documented options, meaning easy to understand when you see them.
* `#chown_uploads=YES` `#chown_username=whoever`
  * Example: `chown_uploads=YES` `chown_username=mostafa`
  * The uploaded files will be owned by "mostafa" on the server, not by anyone else, regardless of who uploaded the file. This is for a dummy user who has no permissions on the server, giving them ownership of the files uploaded to the server. Therefore, any uploaded file will be owned by this user.
* `xferlog_enable=YES` enables logs on the FTP server. The logs are stored in this place: `#xferlog_file=/var/log/xferlog`.
* `xferlog_std_format=YES` uses the standard format for logs.
* `#idle_session_timeout=600` means if the user does not perform any action on the server, the connection will be terminated automatically. You can change this time as you want.
* Idle timeout is important because the FTP server has limited resources. You cannot set the time to infinity because the user will have an open session consuming the server's resources. Therefore, after a specific period, you must set a time limit, and the connection stops if no action is performed.
* `data_connection_timeout=120` Most people enable this option to put a limit for transferring on the FTP server. As in the example, it disconnects every 120 seconds. This affects resuming a transfer; it will disconnect every 120 seconds.

&#x20;

* `#nopriv_user=ftpsecure` You can enable this option to run FTP with non-privileged user permissions because if any attack happens on the FTP server...\
  **Linux security and TCP/IP**\
  **A reminder that we should operate with the least privileges possible.**
* `#async_abor_enable=YES` Stops any abnormal connection.
* `#ftpd_banner=Welcome to blah FTP service.`\
  **Connect to FTP**\
  **What message do you want to appear?**
* Users that are not allowed to log in via FTP. You can add the users you want in this file:\
  `vim /etc/vsftpd/ftpusers`
* Note in this file → `root` is not allowed to log in. But why? Because `root` has full permissions on the system, and FTP is a plain text protocol; it sends everything unencrypted, which is very dangerous. So if you try to log in as root, it will say "Permission denied".
* **Anonymous user:** `/var/ftp`\
  **They are jailed to this location, meaning they cannot go to any other place on the server, unlike local users who can navigate the file system within their home directory, so they are restricted to it.**\
  `cd /` If you log in as a local user, it will take you to this directory (`/`) on the server.
* `#chroot_local_user=YES`\
  **It prevents local users from seeing everything on the server and restricts them to their home directory.**
* `#chroot_local_user=YES`\
  **You must uncomment this line.**
* `#chroot_list_file=/etc/vsftpd/chroot_list`\
  **To jail the users listed in this file, you must also uncomment this line.**
* `vim /etc/vsftpd/chroot_list`\
  **Create a list of the users you want to jail here.**
* `chroot_list`: This file does not exist; you will create it using vim.
* Inside this file, put the users you want to have access only to their home directories. After that, restart the service.

&#x20;

* After creating this file, you might find that the users do not have permissions to see their directories on the server because SELinux is enabled (and you know we haven't studied SELinux). These things must be allowed by SELinux.
  * How to solve this problem? Follow these steps:
  * Allow these things via SELinux:
    * `setsebool -P ftp_home_dir 1`
    * `setsebool -P ftpd_full_access on`
* **In the configuration file, you will add this line between or after these lines. It's up to you:**
  * `allow_writeable_chroot=YES`\
    `chroot_local_user=NO`\
    `chroot_list_enable=YES`\
    `allow_writeable_chroot=YES` (Don't forget to add this line)\
    `chroot_list_file=/etc/vsftpd/chroot_list`
* `chroot_local_user=NO` Set the value of this option to `NO`.
* **After that, restart vsftpd:** `systemctl restart vsftpd`
* In the future, other users you add will not be jailed, only those in the directory.
* **This talk is not allowed in any production environment.**
* There is another solution to this problem, which is considered one of the weaknesses: disable SELinux with this command `setenforce 0`.
* `allow_writeable_chroot=YES` Don't forget also to add this line.
* Regarding the problem with SELinux denying the user login on the FTP server...\
  **500 OOPS: could not read chroot() list file:/etc/vsftpd/chroot\_list Login failed.**
* **Local users have permissions to see all files in this location (`/`) and any directory under it, but they do not have permissions to see what's inside the directories. If you want to give them full access to everything on the server, run this command:**\
  `setsebool -P ftpd_full_access 1`

&#x20;

* &#x20;

&#x20;
