# Managing DHCP and IP Address Assignment

**DHCP Installation**

**DHCP Installation**

bash

```
yum install dhcp
systemctl status dhcpd
systemctl enable dhcpd
```

***

#### **DHCP Definition**

**DHCP (Dynamic Host Configuration Protocol)**\
Definition: It’s a protocol that allows us to give network configurations (like ip address, DNS) to all devices on the network automatically. Specify a server on your network for DHCP. Configure clients to use DHCP server to get network configurations automatically.

***

#### **DHCP Process (DORA) — Discover, Offer, Request, Acknowledge**

**Discover:** Client is configured to get ip from the network so it sends broadcast message specifically on special mac address (ff:ff:ff:ff:ff:ff) on its network so All The machines will receive that message and The purpose for the message is to know where is the dhcp server.

**Offer:** The Dhcp server should reply at the message by giving the client network settings like ip address, net mask, gateway, dns so it send a unicast packet (includes networking settings) to the client that looking for dhcp server.

**Request:** The client accepts the offer and it sends broadcast message to all devices again about accepting that offer.

**ACK:** DHCP server will reply with Acknowledge.

**request**

bash

```
route -n
```

to get the gateway.

***

#### **DHCP Lab**

bash

```
nmcil connection show profile_name
```

**How to know that your device takes ip from dhcp**\
`ipv4.method: auto`

Your thing is meaning that you are from dhcp and by the way it is not meaning that the location is confirmed for you from the from the static you might not do it for him on it by the way possible\[ّ]

bash

```
[root@client ~]# less /etc/sysconfig/network-scrints/ifcfg-ens33
```

If this machine is not working with Network manager you will find a file in this location.

bash

```
root@client ~]# less /var/lib/dhclient/dhcp.lease
```

You will find in it information about what ip it took and from which server: lease file it with all its information this if you are not working manager and took it for a period of time from the network. It will have information for the network that the manager requested from the dhcp server. if you find file.lease that mean the interface configure it to take ip from dhcp server.

bash

```
[root@client ~]# less /var/lib/NetworkManager/dhclient-7e1c02ea-6a75-4fd0-8dee-9c79e7f9dcc3-ens33.lease
```

**Lease file example:**

plaintext

```
lease {
  interface "ens33";
  fixed-address 192.168.21.132;
  option subnet-mask 255.255.255.0;
  option dhcp-lease-time 1800;
  option routers 192.168.21.2;
  option dhcp-message-type 5;
  option dhcp-server-identifier 192.168.21.254;
  option domain-name-servers 192.168.21.2;
  option broadcast-address 192.168.21.255;
  option domain-name "localdomain";
  renew 6 2017/03/18 20:41:57;
  rebind 6 2017/03/18 20:53:26;
  expire 6 2017/03/18 20:57:11;
}

lease {
  interface "ens33";
  fixed-address 192.168.21.132;
  option subnet-mask 255.255.255.0;
  option routers 192.168.21.2;
}
b/NetworkManager/dhclient-7e1c02ea-6a75-4fd0-8dee-9c79e7f9dcc3-ens33.lease
```

**Understanding Lease Time:**\
The address you take from dhcp it's not forever.\
`option dhcp-lease-time 1800;`\
The duration the address is with you 1800 seconds meaning you take more than one lease by the way each period is a lease meaning if you are not configured to take from dhcp it doesn't mean that for you.

`option dhcp-lease-time 86400;`\
Pay attention then that the client will not take the ip for life, he will take it for a specific period, so here is the lease time, meaning the period that the ip that you took 86400 will be with you which is 1440 minutes meaning 24 hours. And it also tells you that you must renew at the specified time.

`renew 2 2018/07/03 20:21:40;`\
And by the way you must look at the date of each lease, why? because you might be the last lease you took from the server for example and thus you here in the file exists. So you must see what your time is exactly?? and see those 3 lines.

plaintext

```
renew 2 2018/07/03 22:52:29;
rebind 3 2018/07/04 08:10:20;
expire 3 2018/07/04 11:10:20;
```

Meaning you must see when exactly you should renew the ip, and if you found the time has ended from dhcp at all, then you know that you are one of those. So here what will happen if you left the device until the `expire 3 2018/07/04 11:10:20` comes? two things will happen, either you will do renew from the dhcp or either you and thus you are not here in the dhcp and thus you will not be able to do renew and you will not use the ip that you took the last time. Meaning originally the DHCP Server will give you an ip and will specify for you an expiration time for it. That the client device and it is the first responsibility which is suppose we have two problems, For example or was not restart was doing renew for the ip went found the dhcp server exists, so what will happen then?

Simply the answer exists in these 3 lines:

plaintext

```
renew 2 2018/07/03 22:52:29;
rebind 3 2018/07/04 08:10:20;
expire 3 2018/07/04 11:10:20;
```

Originally the dhcp tells the client you are supposed to ask me for a new address, and will you continue with the address that you took this or not, not like this yes, it also gives him something called expire. Okay suppose that you came to do renew and didn't find the server? originally supposed the dhcp server configure for it something called the grace period which is the period of grace, meaning that the DHCP Server device when it gives the address to the Client device will tell him if you couldn't communicate with me and get a new address then you have a grace period. I gave you it might be for example 2 hours or 6 hours try during this period that you keep with you the address that I gave you, of course during this period he will be trying to connect to the dhcp server to renew the address as well, okay suppose that you still couldn't do the communicate with the server during this period, then that's it the address yours will have its expiration.

So the Sheikh from third, first the line `renew 2 2018/07/03 22:52:29;` means that at this time he will start doing renew okay and then we look at the line `rebind 3 2018/07/04 08:10:20;`. So the period between these two lines, meaning approximately from 10 at night until morning 8 this is the grace period that you will try to do with it to confirm on the address based on, or to get a new address from the dhcp server, okay suppose didn't find it, then we enter the third line `expire 3 2018/07/04 11:10:20;`. So the period between the second and third line which is two hours, during which you can use the old address that you took from the DHCP Server Configured and its number will end and by the way this value based on this time.

Okay then we have a question here, which is if you are a system admin and working in a company large and you have for example 500 devices connected to a DHCP Server, and suppose then that your working hours are from 8 morning to 6 evening. How should you configure the value exactly what exactly?? The question more difficult, should the values of the dhcp be large or small or what exactly? meaning when I give an ip to the Client I give him ip for a week or months or what exactly? Look you have a problem if you for example gave each device you have a static ip for a period of the week for example, because assuming that you have the users traveling and trying some their devices are not fixed in their place in the company and thus the addresses or the pool you have will finish, and you originally when you do configure for the DHCP you tell him that when someone takes for example an ip he takes from this specific subnet, meaning for example you would be 24/ meaning you give a number of 255 addresses, some of them the gateway ip so you have 253 addresses Maximum number.

Okay also if you made the value based on the renew ip very small for example two hours, then you have the BroadCast will increase a lot, then if you for example have 200 devices and those 200 devices send every two hours BroadCast then you like this will have what is called a BroadCast Storm similar to a DDOS Attack. Meaning like this that you must put respectable values and this value should be according to the number of devices you have and their work will be how also. And the preferred scenario somewhat, is that if your devices are connected via wired, then the renew value should be for example less than 8 hours and make the expire time based on it 16 hours or on it for example 12 hours, okay if the devices then are connected via wireless, then try to make the lease time that is the grace period based on the devices every two hours, and the reason is that the nature of wireless is completely different from the nature of wired at all, its nature is that every while one keeps receive timeout, one disconnects one disconnects and so on and make the renew time based on the devices be every 2.5 or 3 hours.

bash

```
systemctl status dhcpd | request request (visit-storage/cemuragetemvice, emailed, vendor p)
```

**Service status example:**

plaintext

```
eset: disabled)
Active: failed (Result: exit-code) since Mon 2017-03-27 13:35:55 EDT; 37m in ago
```

You will find that there is no configuration file originally.

bash

```
[root@server dhcp]# less /etc/dhcp/dhcpd.conf
```

plaintext

```
# DHCP Server Configuration file. see /usr/share/doc/dhcp-server/dhcpd.conf.example see dhcpd.conf(5) man page
/etc/dhcp/dhcpd.conf (END)
```

**See example configuration:**

bash

```
see /usr/share/doc/dhcp*/dhcpd.conf.example
```

Find a sample from the configuration files existing in this location `/usr/share/doc`. This location has the documentation files for most of the services that when you install the service make a file from these to know how to write the configuration for the service.

bash

```
[root@server dhcp]# less /usr/share/doc/dhcp-server/dhcpd.conf.example
```

Copy the example configuration file named dhcp.conf in its path and create it meaning overwrite.

bash

```
[root@server dhcp-4.2.5]# cp dhcpd.conf.example /etc/dhcp/dhcpd.conf
```

Vim dhcpd.conf come let's open the file and change the settings for it.

**Configuration changes:**

plaintext

```
domain name first thing we change in
[option domain-name "mostafa.com";]
change domain name server and you can search on google and get open dns server
[option domain-name-servers 208.67.222.222, 208.67.220.220;]
you change default-lease-time ex: default-lease-time 7200;
max-lease-time 10800;
```

**default-lease-time (two hours):** supposed after two hours the client should renew y?\
**max-lease-time (three hours):** if you didn't communicate with me after two hours keep the ip with you an additional hour. By the way time is calculated in seconds.

**authoritative:** if you remove the comment, this option will be valid and this mean this official dhcp server.

plaintext

```
#authoritative;
```

this will be useful for you if you have network that has two dhcp servers. there is a problem if two dhcp server don’t work with each other because two dhcp server can give the same ip address to two different clients (conflict will happen) so you have to make one of them is authoritative. Allow all things and cause the things in the comments and modify in them.

**Example dhcpd.conf content:**

plaintext

```
# dhcpd.conf
# sample configuration file for ISC dhcpd
# option definitions common to all supported networks...
option domain-name "example.org";
option domain-name-servers ms1.example.org, ms2.example.org;
default-lease-time 600;
max-lease-time 7200;
# Use this to enable / disable dynamic dns updates globally.
ddns-update-style none;
# If this Dhcp server is the official Dhcp server for the local network, the authoritative directive should be uncommented.
authoritative;
# Use this to send dhcp log messages to a different log file (you also have to hack syslog.conf to complete the redirection).
log-facility local7;
# A slightly different configuration for an internal subnet.
# Use this to send dhcp log messages to a different log file (you also have to hack syslog.conf to complete the redirection).
log-facility local7;
# A slightly different configuration for an internal subnet.
subnet 10.5.5.0 netmask 255.255.255.224 {
  range 10.5.5.26 10.5.5.30;
  option domain-name-servers ms1.internal.example.org;
  option domain-name "internal.example.org";
  option routers 10.5.5.31;
  option broadcast-address 10.5.5.31;
  default-lease-time 600;
  max-lease-time 7200;
}
# Hosts which require special configuration options can be listed in host statements.
# If no address is specified, the address will be allocated dynamically (if possible), but the host-specific information will still come from the host declaration.
host passacaglia {
  hardware ethernet 0:0:c0:5d:bd:9b;
  filename "vmunix.passacaglia";
```

**Subnet configuration explanation:**\
The class that you will define globally above will apply to every subnet that you define for dhcp every subnets will take the same information defined above but the one defined inside this subnet will be specific to this subnet like if you define an ntp server inside a subnet it is specific to the ips inside it then like this change in the configuration as you want for example network ip, the settings inside lease `subnet 10.5.5.0 netmask 255.255.255.224 {` o range that I will control I will start distributing ips from what to what `range 192.168.32.5 192.168.32.250;`

Okay a nice question we started distributing ips starting from `192.168.121.5` and ended at `192.168.21.250`. Pay attention that the people of Network Administration always reserve the Gateway either in the beginning of the subnet or be at the end normal you don't put it in the middle because it can be in the last of the subnet. Some people reserve two and start distributing ips after you leave first 4 and last 4 ips in the first and last of the subnet read.

plaintext

```
option domain-name-servers ns1.internal.example.org;
option domain-name "internal.example.org";
```

The Domain Name and the Domain Name Servers here, you originally don't need them meaning you can delete them. Because you defined them Globally above in the beginning of the file.

Change the gateway for the main one which is the vmware.

plaintext

```
option routers 10.5.5.1;
```

bash

```
route -n
```

Change the broadcast address. The dns I defined globally above already so why here.

bash

```
systemctl restart dhcpd
systemctl status dhcpd
```

We see the server is working without problems.

bash

```
tall -f /var/log/messages
```

and the client there that gives an ip on and is the device there in.

**Log example:**

plaintext

```
Dec 30 08:28:14 server dhcpd[5266]: DHCPDISCOVER from 00:00:29:9c:c4:el via ens33
Dec 30 08:28:15 server dhcpd[5266]: DHCPOFFER on 192.168.32.5 to 00:00:29:9c:c4:el via ens33
Dec 30 08:28:15 server dhcpd[5266]: DHCPREQUEST for 192.168.32.5 (192.168.32.150) from 00:0 c:29:9c:c4:el via ens33
Dec 30 08:28:15 server dhcpd[5266]: DHCPACK on 192.168.32.5 to 00:00:29:9c:c4:el via ens33
```

Go to the client dhcp machine. Like this we see a model of how the dhcp server gave the client an ip.

bash

```
less /var/lib/NetworkManager/dhclient-59afa824-d966-424a-bb92-83ae974c6e5d-enp0s3.lease
```

read.

Look like this at the `option domain-name "ahmed.com";`. See like this it gives the name exactly and regarding the file based on the `less /var/lib/NetworkManager/dhclient-59afa824-d966-424a-bb92-83ae974c6e5d-enp0s3.lease`. We will also find the name of the device that, requested from you, and I have for example named it client.

plaintext

```
Jul 31:33:33 server dhcpd: DHCPREQUEST for 192.168.1.9 from 08:00:27:22:7F98 (client) via emp0s3
```

And of course confirm that when you do DiffCP for Diffigure and also you don't have , the next thing which is choose a suitable time for your network dhcp 2. And also works UDP Connection and that it works on DHCP something it has on port 67 organize but without missing any details.

\
<br>
