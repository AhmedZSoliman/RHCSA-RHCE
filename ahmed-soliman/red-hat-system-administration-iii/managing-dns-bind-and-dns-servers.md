# Managing DNS(Bind)and DNS Servers

&#x20;

## DNS

* **Introduction to DNS**
  * DNS: Resolves the name (it translates the name to an IP).
  * To access resources (servers, machines), it is done through their IPs.
  * In the world right now, we have a lot of IPs, and it’s hard to memorize them.
  * To solve this problem, instead of typing the IP to reach a specific resource, there is a file called `/etc/hosts`.
*   `/etc/hosts`: In this file, we put IP addresses with names locally (meaning the mapping is on each machine).\
    Example:

    text

    ```
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
    ```
* In this way, we have a problem: the file size, because you have to type everything locally on every machine.
  * DNS: a collection of files, and each file has entries, and each entry contains an IP address and a name (it's not an invention).
  * IANA stands for (Internet Assigned Numbers Authority).

&#x20;

* IANA created a hierarchical system to maintain the internet database completely.
* Let’s explain the hierarchical system.

text

```
          [Root DNS Servers]
          (Root ".")
                |
    +-----------+-----------+-----------+--- ...
    |           |           |           |
   com         net         edu         org        ... [Top-Level Domains (TLDs)]
    |           |           |           |
bytebytego.com amazon.com stanford.edu wikipedia.org  [Domains]
    |
  www.bytebytego.com                                  [Subdomains]
```

* There is something called parent name servers. They start with a dot (`.`).
* The dot (`.`) represents the parent name servers.
* Under the dot (`.`), there are Generic Top-Level Domains (TLDs) like `.com`, `.org`, `.net`.
* Under the TLD, there are domains like `google.com`, `yahoo.com`.
* Under the domains, there are subdomains like `mail.google.com`.
* **Note:** Instead of a Generic Top-Level Domain, we can use a Country Code Top-Level Domain (ccTLD):
  * For example: `.uk`, `.us` — each country is given two letters to represent it.
  * Under it, there are domains like `google.eg`, and under them subdomains like the ones above.
* So, we have two DNS hierarchical systems, but in the end, both of them return to the root servers and their replicas.
* All of this information is stored in central databases called Root DNS Servers.
* **Root DNS Servers:** Maintain the DNS database.

&#x20;

* Root DNS Servers are distributed across many countries.
* The number of root servers is 13. Do these servers have the ability to serve all requests (billions of requests) from users all over the world?\
  The answer is: No.
* This problem is solved by using Anycast.
* Anycast is a technology that allows a group of servers to have the same IP address.
* So, you have the ability to replicate each of the 13 root DNS servers.
* So when you request a root server, the system automatically routes you to the nearest one (management of this happens automatically).
* **Note:** `www` is a subdomain.
  * [Subdomain.Domain.Top](https://subdomain.domain.top/)-Level-Domain.
*   Why do we put a dot at the end? We always access websites without writing the dot at the end.\
    Example:

    text

    ```
    [root@client ~]# nslookup yahoo.com
    Server:     192.168.32.2
    Address:    192.168.32.2#53

    Non-authoritative answer:
    Name:   yahoo.com
    Address: 98.137.11.163
    Name:   yahoo.com
    Address: 74.6.231.21
    ```

    It doesn’t make a difference whether you write it or not, because the browser automatically adds it at the end.
* `nslookup domain_name` asks the DNS server for the IP address of a specific domain.
* **Note:** The DNS server shown here is your home router `192.168.32.2`, and the port used is `53`.
*   `nslookup` — type this command and press Enter, then write the DNS (or domain) you want to query.\
    Example:

    text

    ```
    [root@client ~]# nslookup
    > server 8.8.8.8
    Default server: 8.8.8.8
    Address: 8.8.8.8#53
    ```

&#x20;

* `dig domain_name` asks the DNS about a specific domain.
* Record type means the type of the DNS entry.

**BIND DNS Installation**

For the lab test, we will use three machines:

* The first one is the Primary Server.
* The second one is the Secondary Server.
* The third one is the Client for testing.
* We will configure the client to take DNS information from the Primary Server, and the Primary Server will be configured to resolve using a public DNS server on the internet.
* If the client wants to browse any website, it will ask the local DNS server, and the local DNS server will ask the public DNS server.

There are two ways for clients to resolve through a Local DNS Server:

* The first way is called an **Iterative query** (or lookup or resolve).
* The second way is called a **Recursive query** (or lookup or resolve).
* By the way, query, lookup, and resolve all have the same meaning. All three mean: translating the name to an IP (or vice versa).

Remember: DNS starts with the dot (`.`), and the dot represents the Root Servers.

* The number of Root Servers is 13.
* Each one of these servers has replicated versions.
* It is impossible for this small number of original root servers to handle billions of requests, so Anycast technology is used.
* Anycast allows a large group of servers to have the same IP address without problems, and the router sends you to the nearest server automatically.
* Anycast: it means "one to the nearest" — go to the nearest DNS server to you.

&#x20;

* Remember: the 13 Root Servers start from the letter A to M.
  * Under the Root Servers, there are the Top-Level Domains like `com`, `org`, and so on.
  * Under the TLDs, there are Domains like `redhat.com`, `google.com`.
  * Under the Domains, there are Subdomains like `www`, `ns`.

**Iterative Query vs Recursive Query**

✅ **Iterative Query**

* Example:
  * There is a Local DNS Server in a network. Clients ask it for resolving.
  * This Local DNS Server is configured to ask Google Public DNS.
  * So, the client asks the Local DNS Server: "Can you tell me the IP of `www.redhat.com`?"
  * The Local DNS Server asks Google Public DNS: "Do you know the IP of `www.redhat.com`?"
  * Google Public DNS does not know this website because it is not in the `google.com` domain, so it replies: "No."
  * But it tells the Local DNS Server to ask the Root Servers.
  * The Local DNS Server takes this response and sends it back to the client.
  * You returned to the client a reference to ask another server, not the final result.
* Now, the client himself asks the Root Server: "Do you know the IP of `www.redhat.com`?"
  * The Root Server replies: "I don’t know, but I know the `com` domain."
  * So, the client asks the `com` server.
  * The `com` server does not know `www.redhat.com`, but it knows `redhat.com`, so it tells the client to ask `redhat.com`.

DNS

&#x20;

* Then the client asks the `redhat.com` domain: “Do you know the IP of `www.redhat.com`?”
* The `redhat.com` domain replies with the final IP address: `10.165.56.45`.
* **Iterative Query meaning:**
  * The client keeps asking by himself step by step until he finds the result (or may not find it).
  * Each time, he only gets a reference that brings him closer to the final answer.

***

#### **Recursive Query**

* Recursive Query is the opposite of Iterative Query.
* The client asks only the Local DNS Server.
* The Local DNS Server asks the Root Servers.
* The Root Servers reply with references for the Top-Level Domains.
* The Local DNS Server continues asking all servers by itself until it gets the final result.
* Then the Local DNS Server returns the final answer directly to the client.
* In short: the Local DNS Server does all the hard work and gives the client the final answer comfortably.
* Its job is to save the client from all this effort.
* **Note:** For a public DNS server in your company, what is the best way to run it?
  * It will have a very high load if using iterative queries, in my opinion.
* **Note:** Local DNS servers do recursive queries.
  * Keep the server’s capabilities in mind, because if there are many clients, try to have two or three servers to distribute the load.
* Should public DNS servers store the results obtained from local DNS servers?

&#x20;

* Yes, because if another client requests the same thing, the server can give the answer directly without querying again, avoiding extra traffic.
* So, extend the capacity of local DNS to do caching.

**Caching:**

* Every resolve result that the local DNS server obtains is stored (cached) on it.
* Records in the cache are not stored indefinitely.
* Root servers, TLDs, domains, and subdomains all have TTL (Time to Live) for their records.

**Example:**

* A public DNS server resolves a record and sends it to the local DNS server along with the TTL.
* This means the local DNS server can store this record only for the TTL duration (e.g., 1 day), after which it must query again for the updated record.

**Importance of TTL:**

* If the IP of `www.redhat.com` changes but your cached record is expired incorrectly, your DNS server will give wrong results to your users.

**Communication ports:**

* Clients talk with the local DNS server on port 53 UDP to resolve names quickly.
* Local DNS servers talk with public DNS servers on port 53 UDP to resolve names quickly.

## **BIND DNS Service on Linux**

* We will talk about a DNS service called BIND DNS. We will run it on the server.\
  `yum search dns`

&#x20;

* BIND is the most popular DNS server in history.
* BIND depends on some packages:
  * `bind.x86_64`: This is the package for the DNS server. If you install it, you will have the DNS service.
  * `bind-pkcs11-utils.x86_64`: Responsible for DNS security.
  * `bind-utils.x86_64`: Utilities for the client, not for the server.
  * `bind-chroot.x86_64`: Chroot means isolating the binaries and configuration of a specific service in a specific place (isolated environment or "jail").
    * This ensures that if someone attacks this service and succeeds, they remain contained in the environment and cannot escape.
    * This directory is separate from the original system directories.
* If the service supports a chroot environment, it is recommended to enable it.

**Setup BIND (implementation for DNS) with Chroot**

text

```
yum install bind bind-chroot bind-utils
```

**DNS Configuration Files**

text

```
ls /etc/named # press Tab twice for autocomplete
```

* **Main DNS configuration:**\
  `vim /etc/named.conf`
* **Zones (domains):**\
  `less /etc/named.rfc1912.zones`
* By default, this file contains 13 root servers with their IPv4 and IPv6 addresses, allowing access to the root servers:\
  `less /var/named/named.ca`

**Service Names**

* **Package name:** `bind`

&#x20;

* **Service name:** `named`
*   **Start the named service:**

    text

    ```
    systemctl start named
    systemctl status named
    ```
*   **To stop the named service (because we want to run named or BIND with chroot):**

    text

    ```
    systemctl stop named
    systemctl disable named
    systemctl mask named
    ```

***

#### **Running DNS Service After Stopping Normal Named**

* After stopping normal `named`, we will use the named that works in the chroot environment (`bind-chroot` or `named-chroot`).
*   **Note:** The configuration files are not automatically moved to one place; you need to run a script that does it:

    text

    ```
    /usr/libexec/setup-named-chroot.sh
    ```

***

#### **Enabling Named with Chroot Environment**

*   **Run the script and specify your path:**

    text

    ```
    /usr/libexec/setup-named-chroot.sh /var/named/chroot on
    ```
*   Under this directory, you will find a small virtual filesystem, and each directory contains the service files:

    text

    ```
    ls /var/named/chroot/
    ```
* The script places all files in an isolated environment.

***

#### **Start Named Service with Chroot**

text

```
systemctl enable named-chroot
systemctl start named-chroot
systemctl status named-chroot
```

***

#### **Notes**

* **Configuration files that were previously under `/etc` are now under:**

&#x20;

`/var/named/chroot/etc`

* `/var/named/chroot` acts as the root (`/`) for the service.
* **DNS configuration files:**\
  `less /var/named/chroot/etc/named.conf`
* **DNS data files:**\
  `less /var/named/chroot/var/named/named.ca`

#### **Name server config and Caching on Name Server**

* **Directive:** A line or statement in the configuration file.
* **Note:** By mistake, you could accidentally modify `/etc/named` or delete it instead of `/var/named/chroot/etc/named.conf`.
  *   To avoid such issues, we can create a symbolic link from the chroot file to the standard location:

      text

      ```
      rm -f /etc/named.conf
      ln -s /var/named/chroot/etc/named.conf /etc/named.conf
      ```
  * However, this step is usually not needed because the developers configured the chroot environment so that editing files in the normal `/etc` location will automatically affect the chrooted files.
* To keep the configuration file small, you can use `include` to add multiple lines from another file:\
  `vim /var/named/chroot/etc/named.conf`
* **Purpose of `include`:**
  * If you have a section in the configuration, instead of writing it in the main configuration file, you can put it in a separate file and include it.
  * This reduces the size of the main configuration and improves organization.
* **Example:**\
  `vim /var/named/chroot/etc/named.conf`

&#x20;

text

```
options {
    listen-on port 53 { 127.0.0.1; any; };
    listen-on-v6 port 53 { ::1; };
    directory "/var/named";
    dump-file "/var/named/data/cache_dump.db";
    statistics-file "/var/named/data/named_stats.txt";
    memstatistics-file "/var/named/data/named_mem_stats.txt";
    secroots-file "/var/named/data/named.secroots";
    recursing-file "/var/named/data/named.recursing";
    allow-query { localhost; any; };
    ...
};
```

* `options`: Represents the general settings of the DNS server.
* **Note:**
  * The DNS server listens on port 53 UDP. All end-user machines use this port by default for the resolving process. Changing it will cause issues because clients expect DNS on port 53.
  * By default, the DNS server resolves queries to itself (localhost).

**Listening IP Addresses**

* The DNS server can listen on a specific IP or multiple IPs, or use `any` to listen on all interfaces.
* It’s better to dedicate the IPs and not use `any` unless necessary.
* Servers often have multiple interfaces: internal network, DMZ, internet, etc. You can listen on all interfaces by specifying their IPs.
*   **Example:**

    text

    ```
    options {
        listen-on port 53 { 127.0.0.1; 201.201.0.3; };
    }
    ```
* **Important:** Semicolons are required; missing them can cause service failure.
* For IPv6:\
  `listen-on-v6 port 53 { ::1; };`

**Directories and Data Files**

* **DNS service data directory:**

&#x20;

`directory "/var/named";`

* **Note:**\
  `/var/named` = `/var/named/chroot/var/named`\
  `/` = `/var/named/chroot/`
* **Cache file storing resolving results:**\
  `dump-file "/var/named/data/cache_dump.db";`
* **Statistics file (e.g., query time):**\
  `statistics-file "/var/named/data/named_stats.txt";`
* **Memory statistics file (RAM usage):**\
  `memstatistics-file "/var/named/data/named_mem_stats.txt";`

***

#### **Query Permissions**

* **Allow queries from specific clients or subnets:**\
  `allow-query { localhost; 172.16.0.0/16; 10.56.56.5; };`
* By default, the DNS server listens and allows queries from itself.
* **Allow cached queries to specific subnet:**\
  `allow-query-cache { localhost; 201.201.0.1/24; };`
* This allows caching the result for clients in the specified subnet.

***

#### **Security and Recursion**

*   **Enable DNSSEC:**

    text

    ```
    dnssec-enable yes;
    dnssec-validation yes;
    ```
* **Enable recursive lookups for clients:**\
  `recursion yes;`

***

#### **Logging**

*   **Logs are stored in this file:**

    text

    ```
    logging {
        channel default_debug {
            file "data/named.run";
            severity dynamic;
        }
    }
    ```

&#x20;

**Root Server Configuration**

*   Include zone data files:

    text

    ```
    include "/etc/named.rfc1912.zones";
    include "/etc/named.root.key";
    ```
*   Root servers point to the dot (`.`):

    text

    ```
    zone "." IN {
        type hint;
        file "named.ca";
    };
    ```

**Restarting DNS Service After Modification**

*   After any modification in the DNS configuration, you must restart the service:

    text

    ```
    systemctl restart named-chroot
    systemctl status named-chroot
    ```

**Notes**

* `/usr/share/doc` contains sample files for services. If a file is missing, you can copy it from here:\
  `/usr/share/doc/bind/named.conf.default`
* Ensure ownership of the configuration file and all DNS files is set to the `named` user, so the service can run:\
  `chown named.named /etc/named.conf`
* **SELinux context:**
  *   If you copied the file from `/usr/share/doc/bind/named.conf.default`, restore the context:

      text

      ```
      restorecon -R /etc/named.conf
      ls -Z /etc/named.conf
      ```

**Allow DNS Through Firewall**

DNS 13

&#x20;

*   Allow queries on port 53:

    text

    ```
    firewall-cmd --add-service=dns --permanent
    firewall-cmd --reload
    ```

**Client Configuration for Lab Test**

*   Set the DNS server on the client to perform resolving:

    text

    ```
    nmcli connection modify Wired ipv4.dns 201.201.0.3
    nmcli connection down Wired
    nmcli connection up Wired
    ```
* Test by opening any website and check if traffic goes through the DNS server using DNS logs:
* Use `tcpdump` to monitor DNS traffic on port 53:\
  `tcpdump -n udp dst port 53`
* Since DNS is running recursively, you will see many IP addresses being resolved by the server.

#### **BIND Logging**

* `/var/log/messages`
  * This is the main place of system logs. Can the DNS server send its logs to this place? Yes.
* **`rndc`:** This tool enables you to control the DNS server.
* How to enable query log on the DNS server when you run this command all DNS logs are sent to this place `/var/log/messages`:\
  `rndc querylog`
* On the client, visit any website for test.
* On the server: `tailf /var/log/messages`
* **Note:** The process of putting the logs in this place `tailf /var/log/messages` will be exhausted because if you have a lot of users in your network, this file will be huge.

&#x20;

* **Note:** Here, if you run this command before and you run it again, you will see a message telling you we stop sending the logs to this place `/var/log/messages`:\
  `rndc querylog`
* **The main config of name server:**\
  `vim /var/named/chroot/etc/named.conf`
* **DNS logs are types.** There are logs for debugging of the working service, logs for security, logs for network, logs for errors, and logs for end users.
  * When I make a DNS server and it sends logs to the system logs, all these types of logs are sent to the same place or in the same file.
  * The question: Do we keep them all in one place or separate them into files, each file for a specific type? It's better to separate them, e.g., this file for debugging logs and this file for security logs.
* **Developers divide the logs in the config file into channels (categories).**
  * Based on this, you can make a channel and you can name the channel, for example, by your name or something else.
  * We will take an example about how to put query logs in a separated place.
  * Example: I will make a channel named `queries_channel` and after that, I will tell the service to put the logs in any file you want, and you can tell the service to put the time with logs, and this is so useful, and you print the category with logs.
* **Note:** `severity`: The degree of importance or criticality of the log. Type it `dynamic`.
* **After that, you have to enable the category of queries and put inside this category the channel you made.** This specifies where this type of log should be stored.

&#x20;

text

```
channel queries_channel {
    file "data/queries.log";
    print-time yes;
    print-category yes;
    severity dynamic;
};
category queries { queries_channel; };
```

* **As usual! after any modification:**\
  `systemctl restart named-chroot`
* You know the file you configured in the log channel; it is created automatically in this place:\
  `ls /var/named/chroot/var/named/data/`
* `tailf log_file_in_the_channel` This file is supposed to store logs of end users. You will find the file under this location `ls /var/named/chroot/var/named/data/`.
* On the client for test, visit any website.
* On the server, you will find the queries of the user stored in the log file that we configured in the channel.
* You can do the same for security logs and errors logs. Make a channel and identify the category for this channel.
* **Log file of the channel:** This file will grow continuously, and also, note that if the network has many users, the size of this file could become very large, depending on the number of requests from clients.
*   We can control the size of the log file through the main configuration of DNS, but how?

    text

    ```
    channel queries_channel {
        file "data/queries.log" versions 5 size 100m;
        print-time yes;
        print-category yes;
        severity dynamic;
    };
    ```

    `versions 5` means create five versions (copies). When the size of the first file reaches 100MB, it will rotate to the next, and so on.
* **As usual! after any modification:**\
  `systemctl restart named-chroot`

#### **Zones and Forward Lookup vs Reversed Lookup**

&#x20;

* **Notes → in the main configuration file of the DNS server** `named.conf`, you will find a directive called `include`. You can use the `include` to organize the configuration file and it makes the main config very small. This will help you as a system admin during troubleshooting.
  * For example, you can put the logging in a file and you can include it in the configuration. The same for zones.
* **Zone** means domain.
* **This file is loaded with zones or domains of the DNS server:**\
  `vim /var/named/chroot/etc/named.rfc1912.zones`
  * This file has predefined zones like `localhost`, and this zone is repeated for IPv4 and IPv6.
  *   **Forward lookup zone:** Means you ask by name, it replies with the IP.

      text

      ```
      zone "localhost.localdomain" IN {
          type master;
          file "named.localhost";
          allow-update { none; };
      };
      ```
  *   **Reversed lookup zone:** You ask by IP, it replies with the name.

      text

      ```
      zone "1.0.0.127.in-addr.arpa" IN {
          type master;
          file "named.loopback";
          allow-update { none; };
      };
      ```
* **Note:** DNS does two operations → (Forward lookup or query) name to IP and (Reversed lookup or query) IP to name.
* **Note:** There is a difference between recursive query and reversed query; they are not the same.
  * **Recursive query:** The server here goes out and searches on behalf of the client to get the result for him.
  * **Reversed query:** This means the client sends an IP to the server and wants the name.

DNS

&#x20;

* `vim /var/named/chroot/etc/named.rfc1912.zones`
  * As a system admin, you will configure zones or domains to do forward lookup or reversed lookup or both.
* **Zone syntax:** First of all, you write the keyword `zone`, after writing it you will write the name of the zone or domain like `ahmed.com`, and after that write the class of the zone (we have many classes but nowadays we work on a class called `IN` which stands for Internet class). After that, open braces with a semicolon. Inside the braces, identify the type of your server. Is this server master or slave? And after that, put the file that DNS records will be stored in.
*   Try the syntax in an empty file: `vim test`

    text

    ```
    zone "dns.com" IN {
        type master;
        file "test.forward";
        allow-query { 201.201.0.1/24; };
    };
    ```
* **Forward lookup zone diagram.**
  * **Best practice:** Make the name of the zone match the name of the file. If the name of the zone is like `www.ahmed.com`, then make the name of the file like `ahmed.com.forward` if the zone is forward, or `ahmed.com.reversed` if the zone is reverse. This will help you when you do troubleshooting.
* **Syntax for reversed lookup zone?** Ask yourself, what is the subnet of this zone?
  * Example: `10.0.0.10/24`. In this subnet, the first three octets are constant, but the last octet changes.
  *   Focus on the constant part and reverse it in this way:

      text

      ```
      zone "0.0.10.in-addr.arpa" IN {
          type master;
          file "test.reversed";
          allow-query { 10.0.0.0/24; };
      };
      ```

&#x20;

**Reversed lookup zone** is defined this way for the subnet `201.201.0.0/24`.

* **Note:** `.in-addr.arpa` is always appended; you just reverse the network part.
* **Task:** Make a zone for this subdomain `test.ahmed.com` and the subnet `10.15.0.0/24`.
  *   **Forward lookup zone:**

      text

      ```
      zone "test.ahmed.com" IN {
          type master;
          file "test.forward";
          allow-query { 10.15.0.0/24; };
      };
      ```
  *   **Reversed lookup zone:**

      text

      ```
      zone "0.15.10.in-addr.arpa" IN {
          type master;
          file "test.reversed";
          allow-query { 10.15.0.0/24; };
      };
      ```

**Master Name Server**

* **Note:** This place has samples for configuration and data files for the BIND service:\
  `cd /usr/share/doc/bind-9.9.4/sample/`\
  You can use them if you face a problem or if files are missing or corrupted.
*   **Lab test:** Make a zone like this:

    text

    ```
    zone "ahmed.com" IN {
        type master;
        file "/var/named/ahmed.forward"; // if you run the service in chroot environment, so `/` equals `/var/named/chroot`
    };
    ```

    The resolving records for the clients will be stored in this file.\
    `vim /var/named/chroot/var/named/ahmed.forward` (Probably you will find it empty).\
    `cp named.localhost ahmed.forward`\
    `vim /var/named/chroot/var/named/ahmed.forward`

    text

    ```
    $TTL 1D
    @   IN SOA   @ name.invalid. (
                    0       ; serial
                    10      ; refresh
                    1H      ; retry
                    1W      ; expire
                    3H )    ; minimum
            NS  @
            A   127.0.0.1
            AAAA ::1
    ```

&#x20;

* `TTL` (Time to Live): Cache time value by default is one day.
* `IN` (Internet class).
* `SOA`: Start of Authority. It's information about the zone and the DNS server.
  * `name.invalid.`: The name of the DNS server. Write it with a dot at the end.
  * Always put a dot at the end of a fully qualified domain name (FQDN).
* After that, type the admin name who is responsible for this domain.
  * Here, don't type the name in this way `admin@ahmed.com` because it will give a syntax error. So type it in this way: `admin.ahmed.com.`
  * Always put a dot at the end of a fully qualified domain name (FQDN).
* `NS` (Name Server): Here you will remove `@` and you will put the name of the server.
* `A`: It means IPv4 address.

**Example**

text

```
$TTL 10
@   IN SOA   www.ahmed.com. admin.ahmed.com. (
                0   ; serial
                10  ; refresh
                1H  ; retry
                1W  ; expire
                3H )    ; minimum
        NS  www.ahmed.com.
www     A   192.168.32.166
```

* `NS` directive: Very special records. It defines the name server in two steps:
  1. First step: We write the name of the name server.
  2. Second step: We write the IP address associated with that name.
* The `A` record defines the IP in one step, as in the example for `www`.

DNS 20

&#x20;

* Make sure that the owner of bind files is `named` (service user) to be able to run the service without any problems:\
  `chown named:named ahmed.forward`
* SELinux and security context of the files:\
  `restorecon -v ahmed.forward`
* To check the syntax of named configuration:\
  `named-checkconf`
* As usual after any modification:\
  `systemctl restart named-chroot`\
  `systemctl status named-chroot`\
  Example output: `Aug 08 17:58:02 server.dns.com named[5269]: zone ahmed.com/IN: loaded serial 0`
  * It means the zone or domain called `ahmed.com` is loaded.
* On the client, for testing the local DNS server that we configured:
  * `dig` is a tool used to query a DNS server.
  * Example: `dig @201.201.0.4 ns.ahmed.com`\
    This queries the local DNS server for the Name Server (NS) records of `ahmed.com`. The DNS server will give the IP associated with the name.

**Master Name Server with Slave Name Server**

* **Master Name server:** It means primary name server.
* **Slave Name server:** It means secondary name server.
* **Note:** The master name server connects to a switch, and a lot of users connect to that switch. Maybe these users are in the internal network or connected through VPN. The load on this network will be high. So you have two choices.

&#x20;

* **Note:** The first choice: You can increase the resources of the name server, but here you have a problem when the server becomes down for any reason.
* **Note:** The second choice: Have another server work with the master server, and we call it the slave server.
* By the way, the setup of the slave is like the setup of the master.
* So you have to tell the slave where its master is, and you have to tell the master where its slave is, so they can communicate with each other.
* How does the slave get info (records) from the master? The master has a variable called `serial` number in the file that has records. When the serial is modified, automatically the master name server transfers his updates to the slave name server. Note: the updates that happen in the data file that has records.
*   **On the slave server,** install the same packages as on the master server:

    text

    ```
    yum install bind bind-utils bind-chroot
    systemctl disable named
    systemctl mask named
    /usr/libexec/setup-named-chroot.sh /var/named/chroot on
    systemctl enable named-chroot
    systemctl start named-chroot
    systemctl status named-chroot
    ```
* **On the slave server,** modify DNS configuration:\
  `vim /var/named/chroot/etc/named.conf`
  * Modify the `options` like the master name server.
  *   We will take the zone that was made on the master name server config and we will put it here, and we will make some changes.\
      Example (from master):

      text

      ```
      zone "ahmed.com" IN {
          type master;
          file "/var/named/ahmed.forward";
      };
      ```

DNS 22

&#x20;

*   We will change the type from `master` to `slave` (first change).\
    Example:

    text

    ```
    zone "ahmed.com" IN {
        type slave; // first change
        file "/var/named/ahmed.forward"; // we will change this as well
    };
    ```

    * The records file (data file) you don't need to configure it on the slave server because the slave will take it automatically from the master.
    * On the slave server, you will find a directory called `slaves`. This directory will have the data file that is transferred from the master. The path is `/var/named/chroot/var/named/slaves/`.
    *   On the slave server, inside its configuration, tell it the directory that will receive the files from the master server. But you have to identify the data file name (on master) on the slave.\
        Example:

        text

        ```
        zone "ahmed.com" IN {
            type slave;
            file "/var/named/slaves/ahmed.forward"; // the data of this file is like master
        };
        ```
* The configuration is almost done, but there are some other steps.
* **On the master server,** tell the master where the slave is.
  * In the zone that we made on the master server, add an option to it:\
    `allow-transfer { 192.168.43.76; };` // You can type device IP or subnet.\
    This specifies which device is allowable for zone transfer.
* **On the master server,** the process of zone or records transfer to the slave server. You can do it by two ways:
  1. **The first way:** Any modification happens on the master; at the same time, the master will tell the slave about this update. In other words, the master will push or notify or update the slave if any modification occurred.

&#x20;

&#x20;

* **The second way:** The slave by itself will ask (pull) the changes that happen on the master, and this happens periodically (it means, for example, the slave server will ask the master server every hour).
*   **On the master server,** for lab test we will do the first way. In order to do that, we will add in the configuration an option called `notify`. Note: every time the serial number is modified on the master, the master will notify the slave about updates or push updates to the slave.\
    Example:

    text

    ```
    zone "ahmed.com" IN {
        type master;
        file "/var/named/ahmed.forward";
        allow-transfer { 192.168.43.76; };
        notify yes;
    };
    ```
*   **On the slave server,** tell the slave where the master is.\
    Example:

    text

    ```
    zone "ahmed.com" IN {
        type slave;
        file "/var/named/slaves/ahmed.forward";
        masters { 192.168.43.166; };
    };
    ```
* **Note:** All of the files of the named service must be owned by the user called `named` and group called `named`.
*   **On the master,** go to this file and configure the author DNS server (slave) and its IP address (slave). By the way, you will configure it in two steps, like the master.\
    `vim /var/named/chroot/var/named/ahmed.forward`

    text

    ```
    $TTL 1D
    @   IN SOA   ns0.ahmed.com. admin.ahmed.com. (
                    0   ; serial
                    1D  ; refresh
                    1H  ; retry
                    1W  ; expire
                    3H )    ; minimum
            NS  ns0.ahmed.com.
            NS  ns1.ahmed.com.
    ns0     A   201.201.0.4
    ns1     A   192.168.43.76
    www     A   201.201.0.5
    ```

&#x20;

* **As usual!** Any modification you do, you have to restart the service, whether you're on slave or on the master.
* **On the master:**
  * `systemctl restart named-chroot`
  * `systemctl status named-chroot`\
    You might see: `zone ahmed.com/IN: sending notifies (serial 0)` // it means the master is sending notifications or updates right now.
* **On the slave:**
  * `systemctl restart named-chroot`
  * `systemctl status named-chroot`\
    You will find a result telling you that the data file on the master was transferred to the slave, and you will find it in this path: `/var/named/chroot/var/named/slaves/`.
* **On the slave,** when you open the data file that was transferred from master to slave, the file will be a binary file. This gives you a little advantage for fast transfer. But if you want to see the file as plain text, tell the master to send this file as plain text.
  * But plain text is the best because if the master goes down, on the slave you can copy the zones or domains in the data file and transfer it to the rebuilding master (new master).
*   **On the slave,** tell the master to send the data file as plain text:

    * `masterfile-format text;` // through this option inside the `options` block.\
      Example snippet inside `options`:

    text

    ```
    options {
        listen-on port 53 { 127.0.0.1; 192.168.43.76; };
        listen-on-v6 port 53 { ::1; };
        directory "/var/named";
        dump-file "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query { localhost; 192.168.43.0/24; };
        allow-query-cache {localhost; 192.168.43.0/24; };
        masterfile-format text; // through this option
    }
    ```

    After modification: `systemctl restart named-chroot`

&#x20;

* To make sure, see the file; is it still binary or not?
*   **For test lab:** Any modification that happens on the master will automatically be transferred to the slave.

    * Example: On the master, add a record to zones or domains or the data file.\
      `vim /var/named/chroot/var/named/ahmed.forward`\
      We will add `mail` as a subdomain and its IP. Don't forget to change the serial to any number. It doesn't matter; you can put any number if the change happens. But for best practice, put the year, month, and day like this `2024131`. Because when you change the serial, the updates will be pushed to the slave, and what happens is the slave compares its serial with the serial of the master.\
      Example modification:

    text

    ```
    $TTL 1D
    @   IN SOA   ns0.ahmed.com. admin.ahmed.com. (
                    2024131 ; serial // best practice
                    1D  ; refresh
                    1H  ; retry
                    1W  ; expire
                    3H )    ; minimum
            NS  ns0.ahmed.com.
            NS  ns1.ahmed.com.
    ns0     A   201.201.0.4
    ns1     A   192.168.43.76
    www     A   201.201.0.5
    mail    A   192.168.43.165
    ```

    After modification: `systemctl restart named-chroot`

    * Check the update on the slave. If it doesn't change, remove the file and restart the named service.
* **Some concepts:**
  * **Note:** An enterprise network has, for example, 3000 users. When you use DHCP and in DHCP you configure the range. So, to divide the load on two servers (slave and master), open the configuration of DHCP. Configure the first range of IPs and tell them your master server is server one and your slave server is server two. Configure the second range of IPs and tell them your slave server is server one and your master server is server two. So the load will be divided on two servers. So you don't need tools to achieve high availability.

DNS 26

&#x20;

* **Note:** If the client communicates with the master and the master is down, automatically he will use the slave because the slave and master are configured in the client network config.
* **Note:** The master server communicates with the slave server on port 53 TCP. You have to enable these ports on the firewall.
*   **Note:** Data file or the file that has records.\
    Check this file on the slave server:\
    `cat /var/named/chroot/var/named/slaves/ahmed.forward`\
    You'll see values like:

    text

    ```
    2024131 ; serial // best practice
    1D ; refresh
    1H ; retry
    3H ; expire
    3H ) ; minimum
    ```
* **`serial`:** Notifies the slave about the updates that happened on the server.
* **`refresh` (periodic value):** The time that the slave server will wait before asking the master server about the updates.\
  Example: Every 24 hours, the slave server will ask the master server about updates.
* **`retry`:** Suppose that the slave server asks the master server and the master is down. Here, in the example, you will retry after one hour. And note here, after many times of retry and the master still down, the slave will expire the records that he took from the master server after a period of time.
* **`expire`:** Is the time that I declare in it that the data file records that we take from the master are expired. In the example, after a week.
* **`minimum`:** If a client or other DNS server asks your DNS server about a record, e.g., `www.google.com` -> `8.8.8.8`. The DNS keeps this result in your cache for at least, for example, 3 hours (Don't ask before 3 hours are done at minimum).
* **`TTL`:** It means the maximum hours if you want.\
  Example: `TTL 86400;` // 1 day, it means ask me after 24 hours.

#### **DNS Views**

&#x20;

* To understand DNS views.
* Other names for DNS view are split view DNS or split horizon.
* **DNS view:** For each part (subnet or network IP) of your network, you will make it able to access specific resources or records in the DNS server.
* Let's talk about an example to understand this concept:
  * Example: You work in a company, and as a matter of fact, the company network is divided into at least 2 parts or more. A part for DMZ, a part for the internal network, a part for guest network, or a part connected to the internet, and so on.
  * So we need to isolate each part of those through the DNS server, so in the DNS server we will implement views.
  * So when a user comes from your network, this user will have the ability to access specific resources, for example, a domain like `www.ahmed.com` or `mail.ahmed.com`.
  * Note: Each server serves a service. In fact, it will have an IP and name in the DNS server.
  * So for each part of your network, you want it to look at specific resources. You will identify for it, for example, the guest network, make it see services like mail, www, and video conference.
* In this example, we will configure three views:
  * **First view:** If the client comes from the internal network, make him see specific resources or records in the DNS.
  * **Second view:** If the client comes from the guest network, make him see specific resources or records in the DNS.
  * **Third view:** If the client comes from the internet network, make him see specific resources or records in the DNS.
* **Note:** If a client outside the network types `www.ahmed.com`, maybe he goes to a server different than the client inside your network typing the same URL. What I mean, they go to different IPs. And this is the concept of isolation because we want to isolate the parts. But you...

&#x20;

&#x20;

* ...can make the client outside the network and the client inside the network access the same server. The function of the DNS views is to isolate the users; you will lose this if you do that.
* **On the master server:**\
  `vim /var/named/chroot/etc/named.conf`
*   Let's implement a view. Actually, we don't memorize anything, so we will go to a sample file:

    text

    ```
    [root@server sample]# vim /usr/share/doc/bind/sample/etc/named.conf
    ```
*   Take this from this sample config and paste it in the named config of the master server:

    text

    ```
    view "localhost_resolver"
    {
        /* This view sets up named to be a localhost resolver (caching only nameserver).
         * If all you want is a caching-only nameserver, then you need only define this view:
         */
        match-clients    { localhost; };
        recursion yes;

        /* all views must contain the root hints zone: */
        zone "." IN {
            type hint;
            file "/var/named/named.ca";
        };

        /* these are zones that contain definitions for all the localhost
         * names and addresses, as recommended in RFC1912 - these names should
         * not leak to the other nameservers:
         */
        include "/etc/named.rfc1912.zones";
    };
    ```
* **Note:** When you implement BIND, you have two choices. Implement it with views or without views.
  * Example: If you declare a view and you will have a zone inside it, but in `named.conf` if you make a zone and this zone doesn't belong to any view, you made an error. Or in other words, if you make a zone and you put it inside a view, but if you make another zone you should also put it inside a view. If you don't, you will get an error when you restart the service.

DNS 29

&#x20;

* **Note:** In the config of the named service, there is a directive called `options`, and inside this, there are the global settings. But if you configure any of these settings inside the view, what will happen? Anything inside the view that is related to the global settings will overwrite it.
* **Note:** Any view you make, you have to put inside it the zone of the root servers (the dot zone), even if you have 500 views. Because if the DNS can't reach the root servers, you will not be able to do resolving. So you have to delete the dot zone outside the view because we will put it inside the view.
*   **Example about view:**

    text

    ```
    view "internal" { // you can name the view any name you want
        match-clients { localhost; 201.201.0.1/24; }; // put here the subnet you want to match
        recursion yes;
        zone "." IN {
            type hint;
            file "/var/named/named.ca";
        };
        include "/etc/named.rfc1912.zones";
        zone "ahmed.com" IN {
            type master;
            file "/var/named/ahmed.forward";
            allow-transfer { 192.168.43.76; };
            notify yes;
        };
    }
    ```
* **Note:** You can put the dot zone inside a file and include it in every view you make if you want.
* **Note:** Comment this line `include "/etc/named.rfc1912.zones";` because it is inside the view? Actually, you can keep it if you need those standard zones.
* **After any change in the named config:**\
  `systemctl restart named-chroot`
* **To make sure that the process of resolving is working or not:**\
  `dig @201.201.0.5 www.ahmed.com`
* **Let's implement another view on the master server:**\
  `vim /var/named/chroot/etc/named.conf`

&#x20;

*   Example: Let's make another view and we will name it `external`. It has the same info as the previous view, but we have to change the name of the data file.

    *   `file "/var/named/external-ahmed.forward";`

        text

        ```
        cd /var/named/chroot/var/named/
        cp ahmed.forward external-ahmed.forward
        vim external-ahmed.forward // for example, change the IP of www and put any IP.
        ```

    Example view configuration:

    text

    ```
    view "external"
    {
        match-clients { 201.201.0.1/24; };
        recursion yes;

        zone "." IN {
            type hint;
            file "/var/named/named.ca";
        };

        include "/etc/named.rfc1912.zones";
        zone "ahmed.com" IN {
            type master;
            file "/var/named/external-ahmed.forward";
            allow-transfer { 201.201.0.2; };
            notify yes;
        };
    };
    ```
* **After any change in the named config:**\
  `systemctl restart named-chroot`
* Do a `dig`; you will find that it allows the clients that you identified.
* BIND DNS supports a database called `GeoIP Database`, and this is a big database around the world that has info about each city in every country. Because if a user comes from a country like Egypt, he will see specific IPs and hosts, and the same for users in the DNS, you configure the IPs of a specific city inside a view.
* **Note:** It's so important to hide the version of named because named is the most important infrastructure service in the world. The data can be used to attack it.
* You can add a line called `version` inside the `options` in the main config of named.

&#x20;

* Example: `version "za3bola dns";` Hiding the version helps you to secure the DNS.
* **After any change in the named config:**\
  `systemctl restart named-chroot`
* **To see the version of the BIND:**\
  `dig @201.201.0.5 -c CH -t txt version.bind`
  * `-c` = for class
  * `-t` = for type (text)
* **To see the version of the Google BIND:**\
  `dig @8.8.8.8 -c CH -t txt version.bind`

<br>
