# NIC Teaming and Network Bridging: Concepts and Labs

#### **NIC Teaming Concept**

* **Etherchannel**: This is for doing link bundling.
* **High availability**: This is something very important in your network.
* Suppose the link (cable) between the server and the switch disconnects.
* The link can disconnect for maybe 500 reasons.
* For example, the network people want to upgrade their switches.
* People want to do maintenance.
* There are people whose job is to manage the cables between the servers, switches, and routers.
* Problems with the cables can make the server go down.
* So, what is the solution?
* I will ask you: Is it on the server? A network card? I will tell you yes, but it won't be on more than one port. I will tell you no.
* This means you can have more than one link from the same card because it has more than one port, so if one stops, the other works.
* **An Ethernet card (NIC) may have 4 ports.**
* So, the network admin—a difficult person who every day has a reason—will disconnect the server's activities.
* He will disconnect the services on the server.
* Tell this rude man: "Connect the server to two switches by two links." Because when the network admin works on one of their switches, the server does its work through the other switch. You have to tell the admin: "Don't do any activities on two switches at the same time."
* Configure two ports so if one of these ports disconnects, traffic moves automatically to the other port in your NIC.
* Your server may have more than one port, and you want to group them together.
* This is to save you from the problems with the network admin.
* Also, the goal of having more than one link from the same server might be to increase **bandwidth**.
* **The modes of NIC teaming? Why will the switch interrupt it?**
* We have a server with multiple ports on the NIC. How do we manage the links from these ports? So, we have modes or ways.
* **Round-robin**: Sends a packet from the first port, then from the second, then from the third, then starts again from the first. It distributes the load on the ports equally.
* **Active backup**: One port on the NIC will send and receive traffic, and the other ports are backup. For example, if the first port stops, the second works; if the second stops, the third works. Notice that only one is working at a time.
* **LACP (Link Aggregation Control)**:
  * **Both the server and the switch must support LACP.**
  * **LACP collects the ports together and sends traffic on them at the same time.**
  * **But on the switch, we collect all these ports into a port channel with an easy configuration.**
  * **Example: If each port supports one gigabit and I have three ports, then I have 3 gigabits of bandwidth.**
  * **LACP: When you do a port channel, it has to be on the same switch (the links from the server must connect to ports on the same switch).**
* So, I have solved the problem? But all the links are on the same switch. So, if the network admin wants to do an activity on the switch, then everything will disconnect for you. But Cisco solved the problem.
* **By stackable switches**: For example, the two switches are physically separate, but by a cable called "stack wise," instead of each switch being separate, you make the two switches act like one switch. Processing is done through only one of them.
* The solution: Let one link go to the first switch and the third link go to the second switch. So, if the network admin does something in the second switch, you keep working.
  * **But your switches have to support stacking.**
  * **LACP: Used with one switch or stacked switches (they appear to the server as one thing).**
* **Load balance**: Used with standalone servers, and you want to send the traffic between two switches at the same time. It divides the traffic equally between two switches or more, and you don't need any support for anything.

#### **How to configure NIC teaming?**

* **The NIC of the server has more than one port.**
* Example: A virtual interface that takes the two ports and combines them so that you have one exit.
  * By the way, it could be two ports or more.
* A **team device** will be created, and it will have a **virtual MAC**.
* **The virtual interface (consider it as the master) uses two physical interfaces (ports). The physical interface is called a slave.**
* **Here, the virtual interface uses two slaves.**

#### **In Linux, we have two implementations to collect more than one interface together:**

* **Bonding**: Used in RHEL6 and versions before it, and it can also work in RHEL7.
* **Teaming**: Started from RHEL7.
* **Teaming**: Is a **user space** implementation.
  * **User space**: A tool or script can be controlled by the user. There is a special process for it, but for each NIC teaming.
  * **Memory footprint**: The load on the machine is less than bonding.
* **Kernel space**: Implementation in the kernel. If you find a bug, it's more serious because it runs as one process.

#### NIC Teaming Lab

* **To show the profiles on the NICs**:

<figure><img src="../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

* **How to create NIC teaming with a virtual interface**: Use a connection profile type called `team` and a virtual interface called `ifname`. Here, you must add the mode.

<figure><img src="../.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>



* Network Manager, when it configures NIC teaming, must be told the mode.
* Configure the mode in JSON format. JSON stands for JavaScript Object Notation.
  *   Example:

      json

      ```
      {
        "runner": {
          "name": "roundrobin"
        }
      }
      ```
  * When you use special characters, put them between double quotes, like this: `"#Dd" : "asa$"`.

***

We have two implementations to achieve NIC teaming or bundling two or more interfaces together.

How do you tell it that the mode is active-backup? The word "mode" is used with bonding (implemented in the kernel). With NIC teaming, it doesn't use the word "mode"; it uses the word "runner".

**Summary**: With bonding, use the word **mode**. With NIC teaming, use the word **runner**.

***

* **Does the team device add it?**

<figure><img src="../.gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>

***

*   **How to add slaves (physical interfaces) to connect them to the virtual interface in NIC teaming**:

    **A profile called `team0-slave` connects the physical interface (e.g., eno...) to the master device called `team0`.**

<figure><img src="../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>

***

<figure><img src="../.gitbook/assets/image (91).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>

* **Add an IP to the virtual interface**:

<figure><img src="../.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

***

*   When you bring down the NIC teaming (virtual device), the physical interfaces go down.

    When you bring up the NIC teaming or virtual device again, the physical interfaces are still down. So, you must bring up the physical interfaces manually.

<figure><img src="../.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>

***

<figure><img src="../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

*   When you stop a slave (physical interface), the work automatically transfers to another slave.

    Traffic is sent through one interface. As soon as you stop it, the traffic goes to the other interface. This is the requirement for NIC teaming, and this achieves high availability.

<figure><img src="../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

***

*   **To know the states of the virtual interface (ports) and to tell you the active interface or port**:

    **Or**

<figure><img src="../.gitbook/assets/image (97).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

***

*   **The configuration of the team device in your machine**:

    NIC teaming: What will this help you with? When you want to apply the same configuration for NIC teaming on more than one server, you won't have to write commands on each server every time.

    Take a dump from the configuration and import it to all servers.

    * Save the configuration into a file by redirection so you can send it to the other servers.

    <figure><img src="../.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>

***

*   With NIC teaming, for example, if you have two ports on the NIC, you can give priority to one port over the other.

    For example, you have two ports with cables going to different switches. One switch has higher capabilities than the other. Therefore, you will give the port connected to the switch with higher capabilities priority over the other.

    * An option with the NIC teaming device you can set:

    <figure><img src="../.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>

    * **To change the priority of the port in NIC teaming**:

    <figure><img src="../.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>

    * **To know the active port** (in the images, the left empty space is where the active port status is shown).
    *

        <figure><img src="../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

#### **Network Bridging Concept**

*   **The concept of Bridging:** You have a physical host (server) and you put virtual machines on your server. This server has only one physical interface (NIC), and this NIC is connected, for example, to a switch.

    **Every virtual machine has a virtual interface (NIC) connected to the physical interface (NIC).**

    **Can the VM control the physical interface?**

    If the NIC of the VM is down, will the physical interface also go down? **No**.

    If you have 3 VMs, for example, all of these VMs point to the same physical interface.

    **Every virtual NIC for each virtual machine has a different MAC address.**

    So, you need **bridges (software bridge):** If you want to forward traffic to the MAC address of a virtual machine, you need a bridge to manage this. Because when traffic comes to the physical interface of the server, which virtual interface should the traffic be sent to? So, we need something (a virtual switch or multi-port switch) that can manage the process of forwarding.

    **The reasons to have bridges:**

    * **VMs should not control the physical interface of the server.**
    * **When you forward traffic, you must have something (a virtual switch or multi-port switch) that can manage the process of forwarding.**

**Network Bridging Lab**

* **Bridging is similar to NIC teaming.**
* I will make a virtual interface (bridge - virtual switch) that will control the physical interface (NIC) of the machine.

***

* **How to make a bridge:**

<figure><img src="../.gitbook/assets/image (103).png" alt=""><figcaption></figcaption></figure>

*   **How to connect this bridge with a physical interface (NIC):**

    **In example:** Interface name `eno3355` is connected with **br0**, and **br0 is the bridge**.

<figure><img src="../.gitbook/assets/image (104).png" alt=""><figcaption></figcaption></figure>

***

* **To add an IP:**
*

<figure><img src="../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (108).png" alt=""><figcaption></figcaption></figure>



*   The physical interface is connected to the virtual interface. **You must bring it up manually.**

    Remember with teaming: when you bring up the team device, it will not bring up the physical interface. It is the same story here.

<figure><img src="../.gitbook/assets/image (109).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>



***

* **Show the bridges in your machine:**

<figure><img src="../.gitbook/assets/image (111).png" alt=""><figcaption></figcaption></figure>

***

* So, if a team device is connected to a bridge, and you want to make it so that if one interface on the team device fails, the other works... make it.

***

*   Everything we did can be done in seconds using a tool called **`nmtui`**.

    A utility to configure everything from it.

<figure><img src="../.gitbook/assets/image (112).png" alt=""><figcaption></figcaption></figure>

***

**Create a bridge and give an IP to the bridge:**

<figure><img src="../.gitbook/assets/image (113).png" alt=""><figcaption></figcaption></figure>

***

<figure><img src="../.gitbook/assets/image (114).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (115).png" alt=""><figcaption></figcaption></figure>

**Create NIC teaming:**

<figure><img src="../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (120).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (145).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>
