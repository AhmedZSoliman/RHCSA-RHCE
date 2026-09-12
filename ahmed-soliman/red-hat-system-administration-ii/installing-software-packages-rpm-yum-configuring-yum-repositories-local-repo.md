# Installing software packages , RPM ,YUM , Configuring YUM Repositories,local repo

Package Management

### Introduction to Package Management

* All OS have a package manager.
* Developers write software (lines of code) → compiled → binary file.
* A binary file is what you need to run on the machine.
* If you transfer a binary file from one machine to another, it may not work.
* For example, if the file is written in Python, and the target machine doesn’t have Python installed, it won’t run.
* Sometimes, developers use additional libraries to develop the program, and the target machine may not have these libraries.
* Even if Python is installed, if the required library is missing, the program will not work.
* Simply put, a binary file is the only thing the developer gives you to run the program.
* To make it easier for users, developers package binaries with required files.
* These are called packages: collections of binary files and their linked dependencies.
* If you want to install software, you install a package.

### Package Manager Types

Microsoft has two types of package managers:

* **EXE**: the most commonly used installer.
* **MSI**: stands for Microsoft Installer.

Each vendor has its own package manager:

* **Red Hat**: RPM (Red Hat Package Manager)
* **Debian**: DEB (Debian Package Manager)
* **Apple**: DMG (Disk Image)

### The Dependency Problem

A big problem arises sometimes:

* For example, when you install an application on Windows, it may require .NET Framework.
* If .NET Framework is not installed on your machine, the program cannot run.

This problem is called a **dependency**.

**Dependency**: when you install a package, it may depend on another package, which may in turn depend on another package, and so on.

**Dependency hell problem**: you try to install package A, it asks for B, then B asks for C, and so on.

### Why Packages are Necessary

Packages are not bad—they save you from having to install software manually from source code.

With source code, you face two problems: **installation** and **removal**.

#### Installation Problems:

During installation, you must compile the code into binaries and move files to proper locations:

* Executables: /bin or /sbin
* Fonts: /usr/share/fonts
* Configuration files: /etc or other relevant directories

#### Removal Problems:

During removal, problems occur:

* You install app X, then app Y, which depends on X. If you want to remove X but keep Y working, it becomes tricky.

### How Package Managers Solve Problems

A package manager solves these problems:

* Before installation, it checks for dependencies. If all required dependencies exist, it installs; otherwise, it stops.
* Before removal, it checks whether other applications depend on this package. If yes, it refuses to remove it.

All OS that support package managers solve these problems partially, not completely.

### YUM Package Manager

**YUM (Yellowdog Updater, Modified)** solves these problems completely.

#### YUM Server:

* Configured by the admin to serve software to client machines.
* Client machines that request packages are called YUM clients.
* YUM server stores all developer-created packages.
* YUM server also contains **metadata**: XML files listing all packages and their dependencies.

#### YUM Client:

* Requests a package from the YUM server.
* If the server has the package, it sends the package along with dependency information.
* The client installs the package plus all dependencies automatically.

**Important**: YUM uses RPM as the backend and metadata to resolve dependencies before installation. YUM is not an alternative to RPM; it works on top of RPM.

YUM server is essentially a **repository server**. The client must know the YUM server address.

### Basic YUM Commands

#### Search for packages:

bash

```
yum search package_name
```

* This command downloads the latest metadata from the YUM server before searching.

#### Installation ISO:

* The ISO you install contains a directory called `packages`. These packages are used during system installation. The installation depends on these packages, and the ISO also contains metadata files (XML and database files) that store information about the packages. This essentially acts as a local YUM server.

#### Install packages:

bash

```
yum install package_name
```

* Note: during installation, dependencies are installed first.

#### List repositories:

bash

```
yum repolist
```

#### How clients know repositories:

Example repository configuration:

bash

```
[root@localhost ~]# less /etc/yum.repos.d/CentOS-Base.repo
```

Repository configuration example:

text

```
base] 
name=CentOS-SreLeasever - Base 
mirrorlist=http://mirrorlist.centos.org/?release=$reLeasever&arch=$basearch&repo=os&infra=$inbaseurl=http://mirror.centos.org/centos/$reLeasever/os/$basearch/
```

* **Base**: The name of the repository.
* **Mirrorlist**: The address of the server that hosts this repository.

Essentially, a YUM server is a web server that hosts RPM files along with their metadata.

#### Remove packages:

bash

```
yum remove package_name
```

* Note: this removes the package and its dependencies.

#### Get package information:

bash

```
yum info package_name
```

#### Update packages:

bash

```
# Update specific package
yum update package_name

# Update all packages
yum update
```

#### Package Groups:

bash

```
# List all package groups
yum grouplist
```

Example use case: If you want to install all packages related to a development environment (like C, C++, Java, Python), do you want to install them one by one or all together? Definitely all together!

* **Package groups**: When you install a group, it installs all the packages you may need for that purpose, not just the minimal packages to run a single program.

bash

```
yum groupinstall "group_name"
```

#### Find which package provides a file:

bash

```
yum provides file_name
```

### Cache Management

When you install a package, the package is downloaded and kept in your machine.

Edit configuration to keep cache:

bash

```
vim /etc/yum.conf
```

Change the value of the variable from zero to one.

Enabling the `keepcache` option ensures that the downloaded packages are kept in the cache directory after installation, so they can be reused later.

### YUM History and Transactions

All transactions you make with yum are recorded in this file.

bash

```
# List transaction history
yum history

# Note: you can use the transaction id to erase pkg you install it or install pkg you remove it

# View details of specific transaction
yum history info T_id

# Undo a transaction
yum history undo T_id
```

### RPM Package Naming Convention

**Package naming example**: `mailcap-2.1.41-2.el7.noarch.rpm`

* `mailcap`: package name
* `2.1.41`: package version (major version)
* `2`: minor package version (specific release or update)
* `el7`: stands for Enterprise Linux 7 (indicates compatibility with which version)
* `noarch`: compatible with all processors
* `.rpm`: package extension

### Direct RPM Commands

If the RPM file (package) is in your machine, you can use rpm command directly. The package is downloaded in your machine so you don't need the YUM server.

#### Install RPM packages:

bash

```
rpm --install pkg_name
# or
rpm --i pkg_name
# Example:
[root@localhost ~]# rpm -i httpd-2.4.6-45.el7.centos.x86_64.rpm

# Install with verbose output
[root@localhost ~]# rpm -ivh httpd-2.4.6-45.el7.centos.x86_64.rpm
```

#### Remove RPM packages:

bash

```
rpm --erase pkg_name
# or
rpm --e pkg_name
# Example:
[root@localhost ~]# rpm -e httpd
```

#### List installed packages:

bash

```
rpm -qa
```

#### Query package information:

bash

```
# For installed package
[root@localhost ~]# rpm -qi httpd

# For package file (not installed)
[root@localhost ~]# rpm -qpi httpd-2.4.6-45.el7.centos.x86_64.rpm
```

### RPM Dependency Problem

The problem with rpm is dependencies. If you have a package and try to install it, and it depends on other packages not available on your machine, RPM will fail.

Example dependency error:

bash

```
[root@localhost ~]# rpm -ivh httpd-2.4.6-45.el7.centos.x86_64.rpm
error: Failed dependencies:
    httpd-tools = 2.4.6-45.el7.centos is needed by httpd-2.4.6-45.el7.centos.x86_64
    libapr-1.so.0()(64bit) is needed by httpd-2.4.6-45.el7.centos.x86_64
    libaprutil-1.so.0()(64bit) is needed by httpd-2.4.6-45.el7.centos.x86_64
```

Solution: Use yum localinstall to handle dependencies:

bash

```
[root@localhost ~]# yum localinstall httpd-2.4.6-45.el7.centos.x86_64.rpm
```

### RPM Update Commands

bash

```
# Update or upgrade package
rpm -U pkg_name
# Note: If package exists, it will update it; if not, it will install it

# Freshen package (only if older version exists)
rpm -F pkg_name
# Note: Requires an older version of the package to exist
```

### RPM File Queries

#### Find which package owns a file:

bash

```
[root@localhost ~]# rpm -qf /etc/httpd/
httpd-2.4.6-45.el7.centos.x86_64
# This tells you which package installed this file
```

#### List files in a package:

bash

```
# For uninstalled package file
[root@localhost ~]# rpm -qpl httpd-2.4.6-45.el7.centos.x86_64.rpm

# For installed package
rpm -ql pkg_name
```

#### Query specific file types:

bash

```
# Configuration files created by package
[root@localhost ~]# rpm -qc httpd

# Documentation files created by package
[root@localhost ~]# rpm -qd httpd

# Scripts executed during package installation
[root@localhost ~]# rpm -q --scripts httpd | less

# Scripts for uninstalled package
[root@localhost ~]# rpm -qp --scripts httpd-2.4.6-45.el7.centos.x86_64.rpm | less
```

### Force Installation and Security

#### Force install (dangerous):

bash

```
[root@localhost ~]# rpm -ivhf httpd-2.4.6-45.el7.centos.x86_64.rpm
```

**Warning**: This is dangerous because you can install the package without dependencies.

#### Package Verification and GPG Keys

How to ensure that the package is trusted or not before installation:

* Any package coming from RedHat is signed with a GPG key
* Before using RPM packages from RedHat, you must import their GPG key
* The key from CentOS, for example, exists on the machine so that if you download a package, it can be verified against this key

Check installed keys:

bash

```
[root@localhost ~]# rpm -qa | grep -i key
keyutils-libs-1.5.8-3.el7.x86_64
gpg-pubkey-f4a80eb5-53a7ff4b
```

If you delete the CentOS key, packages won't be trusted:

bash

```
[root@localhost ~]# rpm -e gpg-pubkey-f4a80eb5-53a7ff4b
```

Check package signature:

bash

```
[root@localhost ~]# rpm -K httpd-2.4.6-45.el7.centos.x86_64.rpm
httpd-2.4.6-45.el7.centos.x86_64.rpm: RSA sha1 ((MD5) PGP) md5 NOT OK (MISSING KEYS: (MD5) PGP#f4a80eb5)
```

Keys location:

bash

```
[root@localhost ~]# cd /etc/pki/rpm-gpg/
```

### Setting Up YUM Server

#### How to configure YUM Server with YUM client

**How to make yum server**: Without any discussion you need packages and you need to host these packages through HTTP, FTP, or something else to share the package with client.

In this lab we will enable sharing with FTP:

bash

```
# Install FTP server
root@server ~]# yum install vsftpd

# Check FTP status
root@server ~]# systemctl status vsftpd

# Enable and start FTP
root@server ~]# systemctl enable vsftpd
root@server ~]# systemctl start vsftpd
```

FTP hosts and serves the files (packages) from this location to the users:

bash

```
root@server ~]# ls /var/ftp/pub/
```

Remember that YUM server has packages + metadata. You need to create XML files that have info about these packages.

Install createrepo package:

bash

```
root@server ~]# yum install createrepo
```

Create repository metadata:

bash

```
[root@server ~]# createrepo -v /var/ftp/pub/Packages/
```

This creates a directory called `repodata` containing XML files (metadata).

### Configuring YUM Client

Now does the client have the ability to access these files or not? You have to enable FTP client on the client machine:

bash

```
# Install FTP client
yum install ftp
# ftp: is the client package, not server package
```

Check connectivity between client and server:

bash

```
ftp server_ip
# Output: ftp: connect: No route to host
```

If you see this, it means your FTP is not accessible. You need to allow users from outside the machine to communicate with the server through the port.

#### Configure Firewall on Server:

bash

```
# Allow FTP service
[root@server repodata]# firewall-cmd --add-service=ftp

# List allowed services
[root@server repodata]# firewall-cmd --list-all

# Make permanent (survives reboot)
[root@server repodata]# firewall-cmd --add-service=ftp --permanent
```

#### Configure Client Repository:

The client needs repository configuration files to know which servers to contact for packages.

bash

```
# Go to repository configuration directory
root@client ~]# cd /etc/yum.repos.d/
```

Create repository configuration file:

bash

```
root@client yum.repos.d]# vim local-server.repo
```

Basic repository configuration:

text

```
[local-Serv]
name=Local Yum Server
baseurl=ftp://192.168.21.133/pub/Packages/
enabled=1
gpgcheck=0
```

Configuration parameters:

* **baseurl**: The address where RPM files are located
* **enabled = 1 or 0**: 0 means this repository is disabled
* **gpgcheck = 1 or 0**: 0 doesn't check the GPG key

#### Enable GPG Key Checking (Optional):

If you want to enable key checking, copy CentOS keys to FTP server:

bash

```
root@server repodata]# cp /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-* /var/ftp/pub/
```

Then configure client to use GPG checking:

text

```
[Local-Serv]
name=Local Yum Server
baseurl=ftp://192.168.21.133/pub/Packages/
enabled=1
gpgcheck=1
gpgkey=ftp://192.168.21.133/pub/RPM-GPG-KEY-CentOS-7
```

Verify repository configuration:

bash

```
[root@client yum.repos.d]# yum repolist
```

### Source Code Installation

**Source\_Code\_Installation**

Ultimate Guide: INSTALLING A PGM FROM .TAR FILES !!! ([youtube.com](https://youtube.com/)).

The summary: Nobody uses this method for installing anything because it's difficult. Therefore, you start from scratch and may or may not reach it, so nobody uses this method for repair.&#x20;
