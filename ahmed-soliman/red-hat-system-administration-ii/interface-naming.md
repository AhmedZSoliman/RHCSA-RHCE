# Interface Naming

## Interface Naming

&#x20;the name of the **Interface** depends on its location in the **BIOS**. For example, the network card that the BIOS reads as "**Ethernet on board**" means it is a card integrated with the Motherboard.

However, the **PCI Express** Ethernet card takes the letter **'s'** from the last part of the word 'express'.

Of course, you might have a card with one port, for example, or with 4 ports. Of course, if it has one port, that means one interface, so you simply name it normally as **ensX**, where X is the port number.

But if you have a card with more than one port, then just name it as **ensXpY**, where **X** is the number of the **slot** where the card is installed, and **Y** is the port number. So, its name could be like this.

ens1p0\
ens1p1\
ens1p2\
Here, **s** is the number of the **slot**. This will help you greatly because you will have **expected behavior** for each interface.

When you write the command `lspci`, it will tell you how many ports each card has. If it doesn't tell you, then automatically you know it has **one port**.

If you want to know your hard drive information, write this command:

**`sudo smartctl -a /dev/sda`**

And note that you have a package (systemd) that tries to make the kernel see the names **applied** from the BIOS.

However, if you want the kernel to see the old names, do a **reboot**, and then press the letter **`e`** to edit the menu entry. Go to the line that starts with **`linux16`**.

Write:\
`biosdevname=0`

And then write also:\
`net.ifnames=0`

Then press **`Ctrl + X`** to continue.

\
Of course, if you reboot the server, the interface names will return to how they were before. So, you must tell the bootloader not to let the kernel take the names from the BIOS, but instead to take the old names. So, you will edit the file:

**`/etc/grub.conf`**

Or the file:

**`/boot/grub2/grub.cfg`**

Of course, do not forget that after you change the interfaces back to the old names, you must go back and **reconfigure** `nmcli`. For example, you might set an IP for an interface or make it automatic.

If you want to get complete information about an interface, use this command:

**`ifconfig eth0`**

Now, if I ask you to translate this name:

**ens1p2**

This means **port number 3** in **slot number 1**, because the port number starts from **zero**.

The cables that are not organized between the servers or in the rack holding the servers are called **cables spider**.

But you buy a **Patch Panel** as an organizing tool just to make the cables look more organized.

Of course, if it is required from you to stop the **NetworkManager** service and start the old service, which is **network**, how do we do it?\
It is simple. You will go to the path:

**`/etc/sysconfig/network-scripts`**

And create a new file with the name of your interface. You get the interface name from the command:

**`ifconfig -a`**

And this file should be named:

**`touch ifcfg-eth0`**

And you will write inside it:

text

```
NAME=eth0
DEVICE=eth0
IPADDR=192.168.60.200
NETMASK=255.255.255.0
```

```
BOOTPROTO=static
GATEWAY=192.168.60.1
DNS1=8.8.8.8
```

And then:

text

```
systemctl restart network
```

Of course, while you are creating the configuration file for `ifcfg-eth0`, its structure is very simple. It consists of a **variable** and a **value**, and all variables are written in capital letters.

<br>
