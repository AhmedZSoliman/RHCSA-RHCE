# Apache HTTP Server Server,MariaDB

One of the reasons Linux controls the world of servers\
Web service listens on port 80 and The secure service listens on port 443

On them, there is a list of web servers by standard or default ports. Any server? Apache is famous.

Because it is very powerful and a web server, the idea emerged that one server can host multiple websites.

Apache has demons (modules) for many scripting languages like php, python, ruby, perl, supporting many, many programming languages.

Apache is huge because it has a lot of modules. Don't install the supported package you will not use them.

For example:\
You have a site written in Python language, why install the PHP package?

Apache Server + MariaDB\_Basics

***

If you do this, you will be in danger. For example, a vulnerability was discovered in PHP.

Let's install Apache service on the server

```
yum install httpd
systemctl status httpd
systemctl enable httpd
systemctl start httpd
```

Apache on RedHat is called httpd\
Apache on Debian or Ubuntu is called Apache2

There is a package group called basic web server

```
yum groups list
```

Apache web server by default supports html files

On the server, see the modules that come with Apache and each scripting language has too many modules

```
yum search apache | less
```

See php modules

```
yum search php | less
```

On the server, Apache serves or hosts the files to the users from this directory or location

```
ls /var/www/html
```

On the server, allow firewall to allow the users to access this location.

```
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
```

On the client, you can try Apache web server by writing the IP address of the server

Note: a page called testing appeared in front of you. What is important in this page?\
Because it tells you that Apache is up and running, and this page appears if you don't host any files in this directory\
/var/www/html on the server

On the server, cd /var/www/html, if you don't have any files here like html, the testing page will appear

On the server, cd /var/www/html for trying, in this location create a file called index.html and why the page is named index.\
You will know after a while.

***

Test from the client by IP

On the server, the config files of Apache are in this location

```
cd /etc/httpd
```

The main config for Apache is here:

```
vim /etc/httpd/conf/httpd.conf
```

Note: After any modification in the config file, restart httpd

```
systemctl restart httpd
```

On the server, configuration of Apache is divided into three parts

Global Configuration\
Main Server Configuration → main site\
Virtual hosts configuration

On the server, main config for the main server

Remember Apache can host more than one website on the same server based on this:\
the part of config for the main site and the other parts for other websites (virtual hosts)

On the server, The first config for it will be for the main site

It hosts its files in this location:

```
/var/www/html
```

On the server, The first config for it will be for the main site

```
vim /etc/httpd/conf/httpd.conf
```

Let's discover this config

ServerRoot "/etc/httpd"\
It means the config files of Apache are under this location

Listen 80\
It means that Apache listens on this port

Dynamic Shared Object (DSO) Support\
Include conf.modules.d/\*.conf

Used to reduce the lines in the configuration file and include allows you to access other config you want about

Apache Server + MariaDB\_Basics 3

***

```
vim /etc/httpd/conf.modules.d/00-base.conf
```

This file loads most of the modules included with the Apache HTTP Server itself.

Each module does something.

User apache Group apache

User running Apache is called user apache (no permissions) and the same for the group.

Configuration of Apache is divided into three parts

Global Configuration\
Like the user who will run Apache and the listening port.\
It's all the things we talked about before but it will affect other parts of the configs.

Main Server Configuration → main site\
Virtual hosts configuration

Main Server Configuration

Serves files for users under this location: /var/www/html

ServerAdmin root@localhost

Sometimes we open a web page and this web page can't load and at the bottom of the page → please contact ServerAdmin\
and you will find the email of the server admin like this:\
ServerAdmin support@ahmed.com, text him/her if there are errors.

\#ServerName [www.example.com:80](http://www.example.com)

Uncomment this line because in the Global Configuration the server listens on port 80.\
So feel free to remove :80 from this line.

Like this example:\
ServerName [www.ahmed.com](http://www.ahmed.com)

This name is the name of the website that the user will write in their browser.

This name depends on two parts.

Apache Server + MariaDB\_Basics

***

The first part: server name or hostname example: ahmed.com

Second part: DNS → the client should have the ability to resolve the site URL to IP to access the website.

Apache will deny access to the / directory or root file system.

Deny access to the entirety of your server's filesystem. You must explicitly permit access to web content directories in other \<Directory> blocks below.

```
<Directory />
AllowOverride none
Require all denied
</Directory>
```

Further relax access to the default document root:

```
<Directory "/var/www/html">
```

only allow this location.

DocumentRoot "/var/www/html"

This location contains files that the web server serves.

Allow to access this directory.

Relax access to content within /var/[www](http://www).

```
<Directory "/var/www">
text
AllowOverride None
# Allow open access:
Require all granted
</Directory>
```

```
<Directory "/var/www/html">
```

Has options called indexes.

Example: index.html, it means this page is the main page of the website.

Options indexes FollowSymLinks follow symbolic or soft links.

If the files have soft links, Apache will follow them.

AllowOverride None

Are there options you want to override?

Require all granted\
It means this directory you allow to access.

DirectoryIndex looks at the files that their name is index like this.

```
<IfModule dir_module>
DirectoryIndex index.html index.php
</IfModule>
```

Here the order is important because it tells Apache to first look at index.html and if you don't find it, look at index.php and so on.

These files are used to secure Apache.

The following lines prevent .htaccess and .htpasswd files from being viewed by Web clients.

```
<Files ".ht=">
Require all denied
</Files>
```

Is /var/log/httpd/ these files have logs of httpd?

```
less /var/log/access.log
```

This file contains who accessed the server and from which IP.

```
less /var/log/httpd/error_log
```

The errors of Apache.

```
LogFormat "%h %l %u %t "%r" %>s %b "%{Referer}i" "%{User-Agent}i"" combined
```

It means the syntax of the logs.

```
<IfModule mime_module>
```

Allows Apache to detect the extensions of the files that it serves and display an icon for the file based on the extension of this file. Automatically.

▼ Apache\_Server\_Part\_2\
Virtual Host

Apache Server + MariaDB\_Basics 6

***

### &#x20;

We will talk about the virtual host configuration.

How to enable user directories.

On the server go to:

```
vim /etc/httpd/conf/httpd.conf
```

Load config files in the "/etc/httpd/conf.d" directory, if any.

```
IncludeOptional conf.d/*.conf
```

* The idea of include is a great idea because it will decrease the lines in the configuration files.
* Any file inside the conf.d directory and its extension .config will be included automatically in the Apache configuration.
* conf.d: when the files are included, which order will the files be included? By alphabetical order. Order them right.

Example:

```
a-config1
b-config2
c-config3
```

or

```
01-config
02-config
03-config
```

You do this to ease troubleshooting.

On the server:

```
cd /etc/httpd/conf.d
```

```
rootserver conf.d]# ls
```

text\
autoindex.conf mod\_dnssd.conf README userdir.conf welcome.conf

On the server:

```
vim userdir.conf
```

* This module allows Apache to serve HTTP requests (HTTP content) from the users' home directories.
* We will choose a user from the users on the server for a try, let's say "all".
* This module by default is disabled.

```
UserDir disabled
```

* Remove disabled and identify which directory (location) you want to serve.

***

### &#x20;

UserDir public\_html change it to this name.

Means any users, and under the user, this directory will be given options.

```
<Directory "/home/*/public_html">
AllowOverride FileInfo AuthConfig Limit Indexes
Options MultiViews Indexes SymLinksIfOwnerMatch IncludesNoExec
Require method GET POST OPTIONS
</Directory>
```

Here I allow Apache to serve the directory called public\_html. This directory exists for every user in the system.

There is a directive (guidance):

```
vim /etc/httpd/conf/httpd.conf
```

```
<Directory />
AllowOverride none
Require all denied
</Directory>
```

Directory called /: Apache denied any access to anything under this directory or filesystem.

If you enable this module called userdir.conf without telling Apache the directories you want to serve, it will not be able to serve any directory. By default, Apache denies anything under the root filesystem except the directories that you tell it to serve.

Require all granted: Apache is allowed to serve from this location.

```
<Directory "/var/www">
AllowOverride None
# Allow open access:
Require all granted
</Directory>
```

To make Apache serve HTTP requests from the home directory of any user:

```
vim userdir.conf
```

Here I tell Apache to serve from this location, so I remove the \* and I type a specific user.

Apache Server + MariaDB\_Basics 8

***

### &#x20;

```
<Directory "/home/all/public_html">
AllowOverride FileInfo AuthConfig Limit Indexes
Options MultiViews Indexes SymLinksIfOwnerMatch IncludesNoExec
Require method GET POST OPTIONS
</Directory>
```

On the server:

```
su - all
```

This user can create a directory named public\_html inside his home directory.

```
cd public_html
vim index.html
```

Write anything inside this file.

Make sure that the public\_html directory has read and execute permission (enable any user to go inside the directory) for others.

To enable Apache (Apache user: service user) to go inside this directory and have the ability to read.

Besides, you have to change the permissions of the home directory → other.

```
chmod o+rx /home/user
```

I told you before: any modification in Apache configuration, you have to restart the service:

```
systemctl restart httpd
```

On the client, open the browser:

```
192.168.1.5/~ahmed
```

➡️ 💬 192.168.32.157/\~all/

Forbidden\
You don't have permission to access this resource.

This problem appears because SELinux is enabled.

To know the status of SELinux:

```
getenforce
```

Temporarily stop SELinux:

```
setenforce 0
```

On the client, open the browser:

```
192.168.1.5/~ahmed
```

Go to this directory:

```
cd /etc/httpd/conf.d/
```

### &#x20;

Install this package:

```
yum install httpd-manual
```

This manual configuration for Apache (like a man page).

The manual configuration is installed inside this location:

```
cd /usr/share/httpd/manual
```

Manual: It has details about every configuration you want, like what can be done.

On the client:

```
http://192.168.32.157/manual/
```

Virtual hosts\
How to serve more than one website using Apache.

We will add a configuration file inside this directory named conf.d:

```
cd /etc/httpd/conf.d
```

like this:

```
vim 01-vhost.conf
```

virtual hosts in action.

Now Apache can give you virtual hosts using different ways.

The first way: name-based virtual hosts: allows Apache to serve more than one website on the same machine based on name.

Example: If you ask the server for a website called [www.ahmed.com](http://www.ahmed.com), Apache will serve the client from a specific directory, and the same if you ask the same server for a website called [www.all.com](http://www.all.com), Apache will serve the client from a specific directory.

Each website has a specific directory.

Apache uses the same IP address and port but distinguishes between sites by name.

The second way: IP-based virtual hosts: this is supposed to allow access based on subnet.

This way is not flexible in any public environment.

It's better to use name-based virtual host.

Remember if you want to create a virtual host:

We will add a configuration file inside this directory named:

```
cd /etc/httpd/conf.d
```

Apache Server + MariaDB\_Basics 10

***

### &#x20;

```
vim 01-vhost.conf
```

virtual hosts.

We will write the configuration here in this shape.

Syntax like HTML tags.

```
<VirtualHost>
</VirtualHost>
```

Inside, put the guidance you want, like:

```
<VirtualHost _default_:80>
A very special directive serves the Apache default website. It will overwrite the default server configuration.
</VirtualHost>
```

```
<VirtualHost *:80>
```

A very special directive called asterisk \* means any IP.\
It will also overwrite the default server configuration like \_default:80.

```
</VirtualHost>
```

Any IP can access this virtual host.

Identify DocumentRoot: is the place that Apache serves the HTML files from.\
After identifying logs.

```
<VirtualHost>
DocumentRoot /opt/main/www
CustomLog "logs/default-vhost.log" combined
</VirtualHost>
```

/var/www/html is the main website. After this, Apache will do this for the main site.

Apache can access this directory and it overwrites the main server config.

Apache Server + MariaDB\_Basics

***

### &#x20;

```
<Directory /opt/main/www>
Require all granted
</Directory>
```

```
<Directory />
AllowOverride none
Require all denied
</Directory>
```

Example: Apache can access this directory.

```
<VirtualHost *:80>
DocumentRoot /opt/main/www
CustomLog "logs/default-vhost.log" combined
</VirtualHost>

<Directory /opt/main/www>
Require all granted
</Directory>
```

You will create the default directories with the same names, as mentioned in the files.

```
mkdir -p /opt/main/www
```

-p means create parent directories if they don't exist.

Notice www is a directory inside the main directory, which is inside the opt directory. Creating them means create.

```
vim /opt/main/www/index.html
```

```
systemctl restart httpd
```

If you want to create a new virtual host for the experiment,

Take a copy of the first file and make another one similar, but you will change a few things.

```
cp 01-vhost.conf 02-vhost.conf
```

```
vim 02-vhost.conf
```

example:

***

### &#x20;

Don't be surprised if there is a file with this name: [www.html.com](http://www.html.com).

Notes: Apache serves all the hosts from the same directory.

```
mkdir -p /opt/www.ahmed.com/www
```

Create a folder like this.

Now we use name-based virtual hosting, so we will add some things in the configuration like this:

```
<VirtualHost *:80>
ServerName www.ahmed.com
ServerAlias ahmed.com
DocumentRoot /opt/www.ahmed.com/www
CustomLog "logs/ahmed-vhost.log" combined
</VirtualHost>

<Directory /opt/www.ahmed.com/www>
Require all granted
Allow from all
</Directory>
```

```
systemctl restart httpd
```

Go to the client and try like this: You will find it contradictory that the main site works but this site does not. For the client to use the virtual host, it must connect via the name [www.ahmed.com](http://www.ahmed.com).

But the client will not know how to translate this name to an IP.

NO, because we don't have DNS.

On the client, hardcode the web hostname in the hosts file.

On the client, write in the browser:

```
www.ahmed.com
```

Serving multiple sites from one server using the virtual host method.

&#x20;

SSL\
Public Key Infrastructure

The difference between public key and private key.

Any user wants to connect to the machine securely. But How?

Apache Server + MariaDB\_Basics

***

### &#x20;

I will make two simple things on the machine → public and private key.

Private key: is a secret key on the server or client machine only.

Public key: is for any user. It doesn't matter who will take it.

Each private key has its public key.

The server has both public key and private key, but the client only has the public key? (Note: The text seems contradictory.)

If we have data, it's encrypted.

If it's encrypted with one key, you must decrypt it with the other key.

If I encrypt using the private key, I will decrypt using the public key, or vice versa.

If I encrypt using the public key, only the person who has the private key will decrypt it.

You can consider the public key and the private key as a lock, and this lock will not open without these keys.

When the server sends data to the client, it will use the private key, and the client will decrypt it using the public key.

When the client sends data to the server, it will use the public key, and the server will use the private key to decrypt that data that the client sent.

The server has two keys, and the public key of the server is with all the people. When it encrypts, it uses the private key, and the clients decrypt using the public key.

The main problem here is the public key is with anyone, and that means anyone can decrypt the data that the server sends?

When the client wants to connect to the server, the client opens a session with the server.

This session has a session key, meaning the session is encrypted.

The session is encrypted through both public and private key?

Session keys: are random keys. The client and the server agree with each other through an algorithm called Diffie-Hellman (DH).

Apache Server + MariaDB\_Basics 14

***

### &#x20;

DH: is a simple algorithm that allows two machines that haven't known each other to communicate.

Both machines have different session keys.

These session keys are generated randomly.

Each machine tries to make its keys.

Both client and server agree with each other on specific session keys.

This algorithm allows them in the future to renew the key if they want.

Is it just decrypt and that's it? Or did you think the topic is about...? The public key is with anyone...

Watch a video on YouTube about Diffie-Hellman to know how machines communicate very securely with each other.

On the server, I will create private and public keys using this package:

```
yum install crypto-utils
```

How does the client trust the server, and who guarantees that the server is not fake and someone is doing DNS poisoning or an attack like a man-in-the-middle attack?

The public key between the client and the server has to be trusted to make sure that the server is not fake.

Certificate Authority (CA) makes sure that the public key is trusted.

The server will send the public key to the CA, and the CA will put a signature on the public key and send it back to the server. Now the server has a certificate (public key with CA signature).

When the client wants to connect to the server, the server will send the public key with the CA signature.

How does the client know that the CA is valid or not? The CA is known... The browser already knows it.

In your browser, you have more than one CA.

* Open Google Chrome and go to the settings and open certificates, and you will find you will find a lot of trusted authorities.\
  If you have a certificate... (Incomplete).
* The server sends the certification with a fake signature.
* Or the server that sends the certification is fake.
* Or the public key is okay but it's not trusted by a CA. It's up to you; you can accept it or not.
* By myself, I will create a public key and I will put the signature on it, and you can't do that in any production environment.
* We will create our keys and will make sure that the keys are okay.
* On the server write: `genkey`
* When you press enter, the server will ask about the server name, and you can say the name of your website: `genkey www.ahmed.com`
* When you press enter, you will find that there is a package called mod-ssl doesn't exist, and this package allows Apache to enable SSL.
  * `yum install mod_ssl`
* On the server write: `genkey`
*   This will generate private key and certification.

    * This certification will be a self-signed certificate, and it gives you the option about you can send this certification to a CA.
    * Press next. It will ask you about the key size of the certification, and this key will be used to encrypt the session between the client and the server. When the size of the key is big, the session will be secure.
    * If the size of the key is very huge, the process of encryption and decryption will take a lot of time, but use the recommended.



    * Choose the recommended size and wait. It tries to generate random bits to create your public or private key.
    * If you want the server to generate the server very quickly, activate the server by using these commands:\
      ls -lhR /\
      cat /dev/random >> /dev/null\
      tree /
    * Certificate Signing Request (CSR): is a public key that is sent to a certification authority so that it can be signed.
    * After that, it asks you about encrypting the private key or not? If you encrypt it, every time you use it, you will need a username and password, and this process is not practical with Apache because every time you restart the Apache service.
    * After that, we will write the info of the key.

    Country Name (ISO 2 letter code) EG\_ |\
    State or Province Name (full name) CAIRO\_ |\
    Locality Name (e.g. city) NewCairo\_ |\
    Organization Name (eg, company) [AHMED.COM](https://ahmed.com/)\_ |\
    Organizational Unit Name (eg, section) IT\_ |

    It asks you for the country name, write two letters. Server name? Write anything: but okay, it's fine.

    * It will ask you where are the private key and certificate. Take the location of the certificate copy.\
      /etc/pki/tls/certs/[www.ahmed.com.crt](https://www.ahmed.com.crt/)
    * Go to SSL config: vim /etc/httpd/conf.d/ssl.conf

    The certification file in this directory is your public key.\
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt Go to this line.

    It asks you about SSL certificate file.

SSLCertificateFile /etc/pki/tls/certs/[www.ahmed.com.crt](https://www.ahmed.com.crt/)\
SSLCertificateKeyFile /etc/pki/tls/private/localhost.key\
SSLCertificateKeyFile /etc/pki/tls/private/[www.ahmed.com.key](https://www.ahmed.com.key/)



## Restart Apache

systemctl restart httpd

* Allow HTTPS connection on the server.\
  firewall-cmd --add-service=https --permanent\
  firewall-cmd --reload
* Write in the browser:\
  [https://www.ahmed.com](https://www.ahmed.com/)

There is a warning telling you that the CA is untrusted, and this means that the public key is not trusted by the browser.

If you go to the browser, you will find the certification that we made is valid for one month, so we need to renew this certification after one month.

Don't take your private key and send it to a CA.

* After making an SSL certificate for the domain, open the config of the virtual host that you want to add the SSL certificate to.\
  vim /etc/httpd/conf.d/01-vhost.conf

## &#x20;

\<VirtualHost \*:80>\
ServerName [www.ahmed.com](https://www.ahmed.com/)\
DocumentRoot /opt/main/www\
CustomLog "logs/default-vhost.log" combined\
\</VirtualHost>

\<VirtualHost \*:443>\
ServerName [www.ahmed.com](https://www.ahmed.com/)\
DocumentRoot /opt/main/www\
CustomLog "logs/default-vhost.log" combined

\</VirtualHost> \<Directory /opt/main/www> Require all granted \</Directory>\
You will take a copy from the VirtualHost tag and put inside it info like listening on port 443 instead of 80 which we set out.

You can change the logs to SSL logs, and you want to activate SSLCertificateFile and SSLCertificateKeyFile.

\<VirtualHost \*:80>\
ServerName [www.ahmed.com](https://www.ahmed.com/)\
DocumentRoot /opt/main/www\
CustomLog "logs/default-vhost.log" combined\
\</VirtualHost>

\<VirtualHost \*:443>\
ServerName [www.ahmed.com](https://www.ahmed.com/)\
DocumentRoot /opt/main/www\
CustomLog "logs/ahmed-vhost-ssl.log" combined\
SSLEngine on\
SSLCertificateFile /etc/pki/tls/certs/[www.ahmed.com.crt](https://www.ahmed.com.crt/)\
SSLCertificateKeyFile /etc/pki/tls/private/[www.ahmed.com.key](https://www.ahmed.com.key/)\
\</VirtualHost>

\<Directory /opt/main/www>\
Require all granted\
\</Directory>

* Restart the HTTP service.
* Go to the browser and try: [https://www.ahmed.com](https://www.ahmed.com/)
* Main website is working.
* /opt/main/www is the location you specified.
* **That's it**, meaning SSL certificate for virtual hosts, your work is done.
* The virtual host serves HTTP and HTTPS connections.

&#x20;

* We're going to talk about dynamic content.
* Static content: In the past, web pages were static pages. For example, if you have a website for news, I will make a lot of HTML files.
* Dynamic content: We have a database; you can update the data of the website by using it. You don't need to create a lot of HTML files. Needless to say, that happens because of content management systems like WordPress, Joomla.

&#x20;

&#x20;

* **On the server**, we will download WordPress on it.

wget [https://wordpress.org/latest.tar.gz](https://wordpress.org/latest.tar.gz)

tar xvfz latest.tar.gz

* **On the server:** yum install php php-mysql

yum install php-mysql

* firewall-cmd --permanent --zone=public --add-service=http\
  firewall-cmd --permanent --zone=public --add-service=https\
  firewall-cmd --reload
* After installing PHP, vim /etc/httpd/conf.d/php.conf

**This file tells you the directory index is called index.php.**

* **By the way, the name of the Apache user on RedHat is called apache, so you have to change permissions of WordPress from root to apache.**

chown -R apache:apache /opt/main/www/wordpress

* s (Incomplete)

### MariaDB\_Basics

* We have more than one type of database.
* The most famous database is SQL and NoSQL databases.
* SQL: The way it works is based on tables, and each table consists of a group of rows and columns.

It gives you a DBMS, giving you some commands to deal with it.

How to connect?

Deal with the existing database.

* SQL databases have problems with scalability.

Because the table, what is the maximum number of tables it can take? And also the rows.

* Examples of SQL databases: MySQL, PostgreSQL, Oracle Database.

All of these are called SQL databases because they share the same language: SQL (Structured Query Language).

* SQL: is a language used to deal with the database.

&#x20;

* NoSQL: It tells you everything in the world is not just tables. The database will be groups of records; each record will be stored in a file.

If you have 500 million records, throw the first 20 million records on one server and the next 20 million on the next server and so on.

* Still, at the current time, small software deals with SQL databases.
* New systems or software become compatible with NoSQL databases.
* Examples of NoSQL databases like MongoDB.
* SQL database: It may have a role in the coming five years because a lot of programs are written with it.
* We will talk about SQL databases.
* Example: MariaDB (MySQL database). This is the development that happened on MySQL. The performance of MariaDB is better than MySQL.
* MariaDB is compatible with MySQL.
* Most of the tools that run on MySQL, you can run them on MariaDB.
* If the backend of MariaDB is MySQL and you might run it on MariaDB without modifying anything. Regarding the license, the management, and the origin, it is...
* yum install mariadb for the clients because this is a client package.
* yum install mysql

MariaDB is already installed.

In the public repositories, you might get the negative and use it? I have a link like...

* EPEL stands for Extra Packages for Enterprise Linux.

Most of the packages you need to use are stored on it.

Packages on it are free.

&#x20;

&#x20;

* Copy the link and paste here.
  * It will download the package and install it.

\[root@server \~]# rpm -ivh [https://dl.fedoraproject.org/pub/epel-release-latest-7.noarch.rpm](https://dl.fedoraproject.org/pub/epel-release-latest-7.noarch.rpm)

* yum repolist, EPEL should appear on it.
  * You will find on it a large number of packages; almost most of what you want, you will find it.
  * You can do the same for a public repository.
  * Most of what you follow, you will find almost... because they give you many, many packages.
  * yum search mariadb | less
  * The repositories for MySQL are on it.
  * yum install mariadb-server for the servers.
* We have two packages for dealing with the database: one for the client and one for the server.
* MariaDB for the client is a package that allows you to deal with the database as a client, like creating tables or rows.
* MariaDB for the server is the service (DBMS).
* Status of MariaDB:
  * systemctl status mariadb.service
  * systemctl enable mariadb
  * systemctl start mariadb
  * systemctl status mariadb
* The funny thing in the topic is that MariaDB stores its data in this location? Its default location.
* If you want to connect to the DB, type:\
  mysql
* To see what databases do you have?

MariaDB \[(none)]> show databases;

Apache Server + MariaDB\_Basics\
22

&#x20;

To exit from the MariaDB shell, type: exit.\
Any user on the machine has the ability to connect to MySQL without asking them for a username and password, so this setup is insecure.\
After installing the DB server, try to install this DB server through, for example, disable remote login, and anyone who wants to connect to the DB server asks for a username and password.\
mysql\_secure\_installation is a script used to secure this service and is compatible with MariaDB without any problems.\
It asks you about the password of the root. Therefore, originally, there wasn't a password? No, you will enter.\
Enter the passwords.\
Remove anonymous users? So type yes.\
Disallow root login remotely? Type yes.\
Because it's so dangerous. By default, the connection between the MySQL client and MySQL server is plain text because if the root user tries to connect from a machine on the network to the database server, probably someone can sniff the connection because if someone does sniffing, they will see the root password of the DB server.\
There is a difference between the root user on the DB (DB admin) and the root user of the system. So you have to log in as a DB admin from the same machine.\
Yes, do this after something? Any...\
mysql, it will tell you access is denied, so type: mysql -p\
mysql -p\
or\
mysql -u user -p password example: mysql -u root -p\
-p, if it's next to it, you will give it the password immediately.\
This format is unrecommended because someone can see the history in the shell, type these commands.

Apache Server + MariaDB\_Basics\
23

&#x20;

show tables\
use mysql

select \* from user \G;

* **By the way, instead of this script called** mysql\_secure\_installation **you can do this manually, literally. Between, you will see.**
* **Information\_schema and performance\_schema are databases created automatically when you run the database service.**
* **If you want to deal with a specific database:**

show tables\
use db\_name;\
show tables;\
select \* from table\_name;

* **Display it in an organized way.**\
  **Because it won't display it in an organized way in the terminal.**\
  select \* from user \G;\
  It will display them one under the other.
* **After creating a database, you can give permissions on a specific database or table.**

grant delete, update, insert on dbname.tablename to dbusername;

**Example: variables, put them in single quotes after** identified by. Write the password of the DB user.

MariaDB \[(none)]> grant all on shop.\* to 'ahmed'@'localhost' identified by '12345';

**Example:**

* It means you will give it permissions on the DBMS completely.

**The database name.The table name**

MariaDB \[(none)]> grant all on _._ to 'ahmed'@'localhost' identified by '12345';

* **'ahmed' is the user** **'localhost'**\
  **That's the machine he will connect from.**

Localhost is the machine you are on originally.

**IP address of the machine.**

* **System user** is not the same as **database user**.\
  **Be careful, Ahmed.**

&#x20;

&#x20;

* DB users don't have any kind of relationship with system users.
* Create database: create database db\_name
* Choose the database called mysql.\
  And make sure the user you created, for example, is added or not. Look at the user table like this.

MariaDB \[mysql]> select \* from user \G;

* MariaDB \[mysql]> select \* from db \G;

MariaDB \[mysql]> select \* from db \G;\
\*\*\*\*\*\*\*\*\*\*\* 1. row \*\*\*\*\*\*\*\*\*\*\*\
Host: localhost\
Db: mostafa\
User: usermostafa

* MariaDB \[mysql]> select \* from user \G;
* When you create a user or remove a user, all of this is unnecessary without telling the DBMS to reload the privileges. These privileges will be reloaded from the called user that exists in the database called mysql, and these privileges apply when the server is started? Or immediately?
  * You can force the server to do this: MariaDB \[(none)]> flush privileges;
* Try to connect

<br>
