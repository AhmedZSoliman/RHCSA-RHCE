# Networking: Commands, Network Manager (nmcli), IPv6, Troubleshooting, and DNS Configuration

* Now there is no standalone machine.
* Any device connected to a network has an **IP address**.
* **Each device in the network has a physical cable connecting it to the network.**
* **The cable connects to the NIC (Network Interface Card) of the machine.**
* **As a system admin, you must configure the NIC with an IP address. Without it, the machine cannot connect to the network.**
* **Network implementation in Linux has two types:**
  * **Network (old version):** Easy to configure because it uses a file.
  *   **Network Manager (new version):** Allows you to set IP addresses and configure VLAN, Teaming, and Bonding. It has many modules for different implementations.

      **You can manage everything with one command: `nmcli`.**
* **Loopback (software or virtual interface):** Tests if the TCP/IP stack works and allows local services to run on your machine.
* Services like **Apache** or **MySQL** listen on a network interface (NIC) waiting for user connections.
*   **If you do not have a physical interface (NIC), the service cannot work.** The service fails because there is nothing to listen to.

    Every machine has a **loopback interface** with IP **127.0.0.1**, which is local to each machine.

    Here, if you want to run a service locally, it listens on the loopback interface.
* **IP Address (IPv4):** `x.y.y.y`
  * `x` = constant part (Network ID, shared by all devices in the network)
  * `y.y.y` = variable part
  * **Subnet mask:** Identifies the number of devices in the network
* Learn more about IP addresses in a separate lesson.
* **DNS and DHCP** are important for network services.
*   **We have two network implementations:**

    * **Network → legacy version**
    * **Network Manager → allows you to configure any type of network easily**&#x20;



    #### **Network Commands (ifconfig, ip, ifdown, ifup)**

    *   **Display the IP address of the machine:**

        ```bash
        ip addr show
        ```

        or

        ```bash
        ip a s
        ```

        or

        ```bash
        ifconfig
        ```

        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/36b1bbe2-6089-447b-9a24-dba4aaf122a6/a719ca3b-bcc0-49a7-8cc2-d36efbd15104/Untitled.png)

        * The **name of the card** shows its **status (up)**. It supports **broadcast** and **multicast** and is running.
        * **MTU:** The maximum size of a frame that can be sent (e.g., 1500).
        * **inet:** IPv4 address (e.g., 192.168.101.104)
        * **inet6:** IPv6 address
        * **ether:** MAC address of the machine (burned on the NIC)
        * **RX packets:** Received packets
        * **TX packets:** Transmitted packets
    *   **To disable (down) an interface:**

        ```bash
        ifdown interface
        ```
    *   **To enable (up) an interface:**

        ```bash
        ifup interface

        ```

#### **Network Manager (nmcli)**

* **Network Manager uses profiles** to store network configurations.
* **Each connection needs a profile.**
  * Example: You can have profiles called **home**, **work**, or **coffee**.
* **Every machine usually has a single NIC.** When you activate a profile (e.g., home), it applies the settings of that network.
* **Can two profiles work at the same time?** No.

***

*   **The main command for Network Manager:**

    ```bash
    nmcli
    ```

***

*   **To show all profile connections:**

    ```bash
    nmcli connection show
    ```

    * **NAME:** Profile name
    * **UUID:** Unique ID for the profile
    * **TYPE:** Type of connection
*   **To see details of a profile:**

    ```bash
    nmcli connection show profile_name
    ```

***

*   **To delete a profile connection:**

    ```bash
    nmcli connection delete profile_name
    ```

***

*   **To add a new profile:**

    ```bash
    nmcli connection add con-name connectionName ifName interfaceName type connectionType autoconnect yes/no
    ```

    * **interfaceName:** Physical interface to use
    * If the connection type is not defined, **Ethernet is the default**
    * **autoconnect:** Yes → activates automatically, No → activate manually
    * If you don’t define an IP, it will take it from **DHCP**

***

*   **To modify a connection:**

    ```bash
    nmcli connection modify connectionName option
    ```

    * Example: Modify something in the activated connection
*   **To modify IPv4 settings manually:**

    ```bash
    nmcli connection modify connectionName ipv4.method manual ipv4.addresses ipAddress ipv4.gateway gateway ipv4.dns dns
    ```

    * **manual:** Static IP (not from DHCP)
    * **Activate the connection** to apply changes

***

*   **To deactivate a connection:**

    ```bash
    nmcli connection down connectionName
    ```
*   **To activate a connection:**

    ```bash
    nmcli connection up connectionName
    ```

***

*   **Network configuration files are saved in:**

    ```
    /etc/sysconfig/network-scripts/ifcfg-profileName
    ```

    * **ifcfg:** Interface configuration
    * **Each profile has its own interface config**
    * **DEFROUTE:** Yes → The gateway from DHCP becomes your default route

***

* **To edit a connection type (Ethernet/WiFi):**
  * You must have the correct interface for the connection type
  * Example: To make a wireless connection, you must have a WiFi interface

***

* **Every profile must be connected to a specific interface.**
*

    <figure><img src="../.gitbook/assets/image (122).png" alt=""><figcaption></figcaption></figure>

## **Network Troubleshooting (TSHOOT)**

#### **System Admin Steps to Solve Network Issues**

1. **Check if the machine can see the NIC (Network Interface Card).**
   * `ifconfig` → Shows **only running interfaces**
   * `ifconfig -a` → Shows **all interfaces (running and down)**
2. **Check physical connectivity** (e.g., cables or switch ports):
   * If the link is **down**, it could be a loose cable or a switch port problem.
   *   Use:

       ```bash
       mii-tool interface_name
       ```

       * This checks if the NIC and cable are **up and working**.

***

#### **Check Network Manager Profiles**

* Network Manager uses **profiles** to store network settings.
* Each connection needs a profile (e.g., **home**, **work**, **coffee**).
* Usually, a machine has **one NIC**. When a profile is active, it applies the network settings.
* **Two profiles cannot work at the same time.**

**Main command:**

```bash
nmcli
```

**Common `nmcli` commands:**

*   **Show all profiles:**

    ```bash
    nmcli connection show
    ```

    * **NAME:** Profile name
    * **UUID:** Unique ID
    * **TYPE:** Connection type
*   **Show profile details:**

    ```bash
    nmcli connection show profile_name
    ```
*   **Delete a profile:**

    ```bash
    nmcli connection delete profile_name
    ```
*   **Add a new profile:**

    ```bash
    nmcli connection add con-name connectionName ifName interfaceName type connectionType autoconnect yes/no
    ```

    * **interfaceName:** Physical interface
    * If type is not defined, **Ethernet is default**
    * **autoconnect:** yes → activate automatically, no → activate manually
    * If no IP is defined, **DHCP assigns it**
*   **Modify a connection:**

    ```bash
    nmcli connection modify connectionName option
    ```
*   **Modify IPv4 settings manually:**

    ```bash
    nmcli connection modify connectionName ipv4.method manual ipv4.addresses ipAddress ipv4.gateway gateway ipv4.dns dns
    ```

    * **manual:** Static IP (not from DHCP)
    * **Activate the connection** to apply changes
*   **Deactivate connection:**

    ```bash
    nmcli connection down connectionName
    ```
*   **Activate connection:**

    ```bash
    nmcli connection up connectionName
    ```

**Configuration files location:**

```
/etc/sysconfig/network-scripts/ifcfg-profileName
```

* **ifcfg** = interface configuration
* **Each profile has its own interface config**
* **DEFROUTE:** yes → gateway from DHCP becomes default route
* To change connection type (Ethernet/WiFi), you need the correct interface.

***

3. **Check the IP address and subnet:**

* Make sure the machine **has a valid IP** and that it matches the subnet mask.
  * Example: **Netmask /32** → network has only **one host**
*   Commands to check IPs:

    ```bash
    ifconfig | grep inet   # show all IPs (IPv4 & IPv6)
    ifconfig | grep "inet" # show IPv4 only
    ifconfig | grep "inet6"# show IPv6 only
    ```
*   **Check routing and errors:**

    ```bash
    route -n
    ```

## **Network Troubleshooting and DNS Configuration in Linux**

#### **1. Check Network Connectivity**

* If the interface is **up with IP**, but `ping` says **network unreachable**, check the **subnet mask**.
* Example: `/32` netmask allows **only one host**.
*   To display IP addresses:

    ```bash
    ifconfig | grep inet    # all IPv4 & IPv6
    ifconfig | grep "inet"  # IPv4 only
    ifconfig | grep "inet6" # IPv6 only
    ```
* **Routing Table:**
  * Every device that uses IP has a **routing table** (not only routers).
  * **Routing table:** Decides which interface to send a packet through.
  * **Packet forwarding:** Packet comes from one NIC and goes out through another (like a router).
  * **Gateway:** The path to reach other networks.
  * **Default gateway:** Used if there is no specific route.
* **Commands to manage routes:**
  *   Add a route:

      ```bash
      ip route add ip/subnet via gateway_ip
      ```
  *   Delete a route:

      ```bash
      ip route del ip/subnet
      ```
  *   Add default gateway:

      ```bash
      ip route add default via gateway_ip
      ```

***

#### **2. Configure DNS (Domain Name System)**

* DNS allows your machine to resolve **hostnames** to **IP addresses**.
* Important files:
  * `/etc/hosts` → Local hostname-to-IP mapping (checked **first**).
  * `/etc/resolv.conf` → DNS servers for resolving domain names (checked **second**).
* Example:
  * You have many servers with names and IPs. If the network is small, you can add all names and IPs in `/etc/hosts`.
  * For large networks, use a **local DNS server**.
* **Network Manager** handles DNS automatically.
  * If you configure DNS manually while using Network Manager, it may overwrite your changes.
  *   To add multiple DNS servers:

      ```bash
      nmcli connection modify connectionName +ipv4.dns "8.8.8.8"
      ```

***

#### **3. Hostname Configuration**

* `hostname` → Shows the current hostname.
* `hostname -f` → Shows the fully qualified domain name (FQDN).
  * Failure occurs if the machine cannot resolve its name using `/etc/hosts` or `/etc/resolv.conf`.
*   **To fix hostname resolution:**

    ```bash
    vim /etc/hosts
    ```

    * Map IP to hostname and FQDN.
    * Example: `192.168.1.10 gw.mostafa.com gw`
*   **Changing Hostname Permanently:**

    ```bash
    hostnamectl set-hostname newhostname
    ```

    * No restart needed.
    * To see the change in prompt, **log out and log in**.
*   **Temporary hostname change:**

    ```bash
    hostname temporaryname
    ```

    * This will **not survive reboot**.
    * The kernel reads `/etc/hostname` on boot for permanent hostname.
    * Do **not edit `/proc` files** manually; they are kernel-managed.

***

#### **4. Notes**

* The hostname is like a personal name; FQDN is like full family name.
  * Example:
    * Hostname: `Ahmed`
    * Domain name: `soliman.mohamed`
    * FQDN: `Ahmed.soliman.mohamed`
* Network Manager is **the main tool** in RHEL7+ to manage all interfaces, IPs, and DNS.
  * Manual changes outside Network Manager may be overwritten.
  * Always use Network Manager if possible.
* Always check `/etc/hosts`, `/etc/resolv.conf`, and Network Manager settings when troubleshooting DNS and hostname issues.

#### **Another Way to Temporarily Change the Machine Name**

* You can temporarily change the hostname using information from **`/proc`**.
  * This file contains information that the **kernel knows about your machine**.
* The file **`/etc/hostname`** shows the hostname that the kernel uses.
  * Sometimes this file may **not exist**.
* **Can you edit this file directly?**
  * No, it may **not allow changes**, because these files are **kernel-managed**.
  * Redirection may not work here, but reading the file is allowed.
* This method is **temporary**; the hostname will **reset after reboot**.
