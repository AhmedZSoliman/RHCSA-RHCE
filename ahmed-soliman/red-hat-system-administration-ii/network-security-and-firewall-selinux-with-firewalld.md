# Network security and firewall , SELinux with Firewalld

## Computer Security and Firewalls

***

### 1️⃣ Computer Security

* **Definition:**\
  Computer security is the protection of **computer systems**—their hardware, software, information, and services—from **theft, damage, disruption, or misuse**.
* **Importance:**\
  Ensuring computer security is an **essential task** for every system administrator.

***

### 2️⃣ Firewall

* **Definition:**\
  A firewall is a **network security system** that monitors and controls **incoming and outgoing network traffic** based on configured **security rules**.
* **Purpose:**\
  Firewalls prevent unauthorized access while allowing legitimate communication.
* **Basic Setup:**\
  A firewall is a **basic security feature** that you can configure immediately after installing the operating system.

***

### 3️⃣ firewalld in RHEL

* The `firewalld` service provides a **firewall in Red Hat Enterprise Linux**.
* It is **automatically enabled during installation**.

#### Managing firewalld

1. **Check the current status of firewalld:**

```
systemctl status firewalld
```

2. **Enable firewalld** (start it automatically at boot):

```
systemctl enable firewalld
```

3. **Start firewalld** (if not running):

```
systemctl start firewalld
```

4. **Verify firewalld is running and enabled:**

```
systemctl status firewall
```

### How Firewalls Work

* A firewall **filters network traffic**.
* Traffic can be **incoming (to the server)** or **outgoing (from the server)**.
* Firewalld organizes traffic using **Zones** or **Profiles**.
* Inside each zone, there are **Rules** that define which **packets** are allowed to pass.
* Each zone has its **own set of rules**, controlling traffic separately.

A **Firewall Zone** is a set of rules that control the entry of packets into the system.

Each zone has specific settings for the system, and you can assign a particular zone to each interface or network.

You can also change the zone or its rules as needed to define the behavior of packets between different areas.

In short, a **zone** defines the "space" or "area" within the firewall, and the **rules** determine what is allowed or blocked within that area.

***

**Packet Classification Process:**

1. **Source and Interface Classification:**
   * A packet entering the server can be classified based on:
     * Its **source IP** address.
     * The **interface** it is coming through.
2. **Protocol Classification:**
   * The packet can also be classified according to the **protocol type**, such as:
     * NFS
     * FTP
     * HTTP
3. **Connection Classification:**
   * The packet can be further classified based on its **connection type**.
4. **Zone Assignment:**
   * After classification, the packet is directed to the **appropriate zone**.
   * Each zone has specific **rules** and is associated with:
     * **Services**
     * **Ports**
     * These rules can be configured to either allow or block the packet, depending on whether the zone is **open** or **closed**.

<figure><img src="../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (141).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (143).png" alt=""><figcaption></figcaption></figure>

| **ommand** | **Description** |
| ---------- | --------------- |

| `firewall-cmd --get-zones` | Display all zones on the server. |
| -------------------------- | -------------------------------- |

| `firewall-cmd --get-default-zone` | Display the default zone on the server. |
| --------------------------------- | --------------------------------------- |

| `firewall-cmd --list-all` | Display all the rules on the server's firewall. |
| ------------------------- | ----------------------------------------------- |

| `firewall-cmd --get-services` | Display all services configured in the firewall. |
| ----------------------------- | ------------------------------------------------ |

| `firewall-cmd --info-service=ssh` | Show information about the SSH service. |
| --------------------------------- | --------------------------------------- |

| `cd /usr/lib/firewalld/` | Access the firewall configuration folder on the server. |
| ------------------------ | ------------------------------------------------------- |

| `cd /usr/lib/firewalld/zones/` | Access the zone configuration files folder in the firewall. |
| ------------------------------ | ----------------------------------------------------------- |

| `cd /usr/lib/firewalld/services/` | Access the firewall service configuration files folder. |
| --------------------------------- | ------------------------------------------------------- |

| `cat ssh.xml` | View the SSH service configuration file. |
| ------------- | ---------------------------------------- |

| `firewall-cmd --set-default-zone=home` | Set the default zone to 'home'. |
| -------------------------------------- | ------------------------------- |

| `firewall-cmd --new-zone=hanizone --permanent` | Create a new zone called 'hanizone' permanently. |
| ---------------------------------------------- | ------------------------------------------------ |

| `firewall-cmd --reload` | Reload the firewall configuration after changes. |
| ----------------------- | ------------------------------------------------ |

| `firewall-cmd --set-default-zone=hanizone` | Set the default zone to 'hanizone'. |
| ------------------------------------------ | ----------------------------------- |

| `firewall-cmd --add-service=ssh` | Add the 'ssh' service to the firewall zone. |
| -------------------------------- | ------------------------------------------- |

| `firewall-cmd --add-port=22/tcp --permanent` | Open port 22/tcp permanently on the firewall. |
| -------------------------------------------- | --------------------------------------------- |

| `firewall-cmd --reload` | Reload the firewall after adding port 22/tcp. |
| ----------------------- | --------------------------------------------- |

| `firewall-cmd --remove-port=22/tcp --permanent` | Remove port 22/tcp permanently from the firewall. |
| ----------------------------------------------- | ------------------------------------------------- |

| \`nmcli connection dolphinnovm1 | grep zone\` |
| ------------------------------- | ----------- |

| `nmcli connection modify dolphinnovm1 connection.zone public` | Modify the connection zone to 'public'. |
| ------------------------------------------------------------- | --------------------------------------- |

| `nmcli connection down dolphinnovm1` | Bring the 'dolphinnovm1' connection down. |
| ------------------------------------ | ----------------------------------------- |

| `nmcli connection up dolphinnovm1` | Bring the 'dolphinnovm1' connection up. |
| ---------------------------------- | --------------------------------------- |

| `cd /etc/firewalld/` | Access the firewall directory. |
| -------------------- | ------------------------------ |

| `cd /etc/firewalld/zones/` | Access the zones directory in the firewall. |
| -------------------------- | ------------------------------------------- |

| `cd /etc/firewalld/services/` | Access the services directory in the firewall. |
| ----------------------------- | ---------------------------------------------- |

| \`firewall-cmd --help | grep add-port\` |
| --------------------- | --------------- |

| `firewall --config` | View the firewall configuration. |
| ------------------- | -------------------------------- |

| `vim /usr/lib/firewalld/` | Open the firewall configuration folder with vim. |
| ------------------------- | ------------------------------------------------ |

## **Selinux with firewalld**

| **Command**                                                   | **Description**                                                                                             |
| ------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| <p></p><p>he command <code>semanage port -l | head</code></p> | <p><code>semanage port -l</code>: Lists all ports with their SELinux context labels.</p><ul><li> </li></ul> |
| \`semanage port -l \| grep ssh\`                              |                                                                                                             |
| `semanage port -a -t http_port_t -p tcp 9595`                 | Add port 9595 with the `http_port_t` label for TCP protocol.                                                |
| `semanage port -l -C`                                         | Display the configured ports that have been labeled.                                                        |
| `semanage port -m -t ssh_port_t -p tcp 9595`                  | Change the label for port 9595 to `ssh_port_t` for TCP protocol.                                            |
| `semanage port -d -t ssh_port_t -p tcp 9595`                  | Remove the label `ssh_port_t` from port 9595 for TCP protocol.                                              |







