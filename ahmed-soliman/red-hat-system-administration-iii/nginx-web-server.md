# Nginx Web Server

#### **Part 1: Introduction to NGINX**

**C10K Problem:**

* Difficulty for web servers handling many simultaneous users.

**NGINX:**

* Created to efficiently manage high traffic and many incoming requests.

**Scaling Issue:**

* Multiple web servers are needed as traffic grows.

**Load Balancer:**

* Distributes user requests among servers to decide which one responds.

**Proxy:**

* Nginx acts as a proxy, receiving requests and forwarding them to the correct server.

**Algorithm:**

* Load balancer uses a configurable algorithm to share requests across servers.

**Load Balancer (LB):**

* A device or service that distributes incoming network traffic across multiple servers dynamically.
* Ensures no single server is overwhelmed, improving performance and reliability.

**Proxy Server:**

* Acts as an intermediary between end users and web servers.
* Separates users from the websites they access, providing security, caching, or traffic management.

**Flow in the Diagram:**

* Users send requests → go through the Proxy Server / Load Balancer → traffic is distributed among multiple backend Nginx servers.

**Caching:**

* When a website article is requested by millions of users, generating it for each request is inefficient.
* Since the content is static, Nginx can cache a single copy of the article.
* By serving this cached version to all users, Nginx reduces server processing, improves performance, and lowers server load.

***

#### **Part 2: Core Concepts & Use Cases**

**Caching Server:**

* Sits between users and a web server to improve speed and efficiency.
* Stores (caches) copies of frequently requested web content.
* When a user makes a request, the caching server checks its stored content:
  * If the content is available, it returns it immediately.
  * If not, it retrieves the content from the main web server, delivers it to the user, and saves a copy for next time.

**Secure Entry Point (Protected Proxy Server):**

* Helps shield multiple sensitive backend servers.
* All incoming traffic goes through this single security gateway, keeping the backend systems hidden and protected from attackers.
* Instead of exposing many sensitive servers to the internet, you use one secure front server that acts as the only entry point.
* All traffic—good or bad—must pass through this single gateway, which protects and hides the backend servers from attackers.

**NGINX in DevOps/Kubernetes:**

* In DevOps practice, NGINX is used as an **Ingress Controller** for our Kubernetes cluster.
* It performs the roles of both a load balancer and a reverse proxy, allowing you to leverage web server features inside the cluster.
* An Ingress Controller is simply a Pod inside the cluster.
* To make it accessible from outside the cluster, it requires a **Service of type `LoadBalancer`**.
* This Service forwards incoming traffic (e.g., from the internet) to the Ingress Controller Pod(s).
* The Ingress Controller then inspects each request and routes it to the correct Service within the cluster.

**Cloud Load Balancer:**

* In cloud environments, the LoadBalancer often functions as a reverse proxy.
* The cloud LoadBalancer serves as the entry point from the public internet to the cluster.
* The Ingress Controller is a Kubernetes-native reverse proxy that understands Kubernetes Services and Ingress rules.
* However, it operates at a different layer than the external cloud LoadBalancer—external (cloud) vs. internal (Kubernetes).

**Component Purpose:**

* **Cloud Load Balancer (e.g., ELB/ALB):** Forwards traffic to your Ingress Controller Pods inside the cluster.
* **Ingress Controller (e.g., NGINX, Traefik):** Routes traffic inside the cluster based on Kubernetes ingress rules (host/path).

**Key Features and Use Cases of NGINX:**\
NGINX’s architecture and flexibility make it suitable for various use cases:

1. **Web Server:** NGINX can serve both static and dynamic content. It’s highly optimized for delivering static files like HTML, CSS, and images very quickly.
2. **Reverse Proxy:** It acts as an intermediary between client requests and backend servers, offering features like caching, compression, and security.
3. **Load Balancer:** NGINX can distribute incoming requests across multiple servers, balancing the load to improve availability and reliability, and handling traffic spikes efficiently.

**Event-Driven Architecture:**

* Asynchronous model.
* Deal with all requests as one thread so it doesn’t do a new process to each connection so that allow to deal effect with concurrent connection.

**NGINX vs. Apache:**

* **NGINX uses an event-driven based model.**
* **APACHE HTTP SERVER PROJECT uses a process-based model.**

**Use Cases:**

* **When to use NGINX:** When high performance, scalability, and serving static content efficiently are key priorities.
* **When to use Apache:** When your application requires dynamic content management, complex configuration, or compatibility with legacy systems.

***

#### **Part 3: Installation & Basic Configuration**

**Installation Steps:**

**For Linux (Ubuntu/Debian):**

1.  Update your package index:

    bash

    ```
    sudo apt update
    ```
2.  Install NGINX:

    bash

    ```
    sudo apt install nginx
    ```
3.  Start NGINX:

    bash

    ```
    sudo systemctl start nginx
    ```
4.  Enable NGINX to start on boot:

    bash

    ```
    sudo systemctl enable nginx
    ```

**For Linux (CentOS/RHEL):**

1.  Update your package index:

    bash

    ```
    sudo yum update
    ```
2.  Install NGINX:

    bash

    ```
    sudo yum install nginx
    ```
3.  Start NGINX:

    bash

    ```
    sudo systemctl start nginx
    ```
4.  Enable NGINX to start on boot:

    bash

    ```
    sudo systemctl enable nginx
    ```

**Post-Installation Configuration (CentOS/RHEL):**

*   **firewalld:** A dynamic firewall service used in CentOS. It provides an easy way to manage network traffic and enforce security policies by allowing or blocking traffic to specific ports and services.

    bash

    ```
    sudo firewall-cmd --permanent --add-service=http
    sudo firewall-cmd --permanent --add-service=https
    sudo firewall-cmd --reload
    sudo firewall-cmd --list-all
    ```
*   **SELinux:** Security-Enhanced Linux (SELinux) provides an additional security layer for Linux systems. SELinux can block NGINX from functioning properly if not configured correctly.

    bash

    ```
    sudo setsebool -P httpd_can_network_connect 1
    ```

**NGINX Configuration Structure:**

* Nginx configuration files is plugining a gorging file, which allows the user to configure the configuration files.
* `/etc/nginx` – Main configuration directory.
* `/etc/nginx/nginx.conf` – Main configuration file, controls global settings for nginx services.
* `worker process`: requests numbers at the same time.

**Configuration Contexts:**\
Configuration files are divided into sections:

1. **Main context:** Global settings like worker processes.
2. **Events context:** Handles connection processing.
3. **HTTP context:** Configures HTTP server behavior.
4. **Server context:** Defines settings for virtual hosts (server blocks).
5. **Location context:** Specifies how to process requests for specific locations or URLs.

**Key Directives in `nginx.conf`:**

nginx

```
user nginx;
worker_processes auto;
...
events {
    worker_connections 1024;
    ...
}
http {
    # Define a cache zone named "my_cache" with a size limit of
    # 10 MB
    proxy_cache_path /var/cache/nginx/my_cache levels=1:2 keys_zone=my_cache:10m max_size=100m;
}
```

* `user`: which user will run nginx server.
* `worker_processes`: auto sets based on cpu cores.
* `worker_connections`: specify max numbers concurrent connections that the one server can deal with.

**Understanding `worker_processes` and `worker_connections`:**

* `worker_processes` Directive:
  * More processes → More Concurrent Connections.
  * Set `worker_processes` to the number of CPU cores for optimal performance.
* `worker_connections` Directive:
  * Defines how many simultaneous connections each worker can handle.
  * `max_clients = worker_processes * worker_connections`.

**Configuring Logging:**

*   **Access logs:** Store details about requests made to the server.

    nginx

    ```
    access_log /var/log/nginx/access.log main;
    ```
*   **Error logs:** Record details about errors encountered while processing requests.

    nginx

    ```
    error_log /var/log/nginx/error.log warn;
    ```

**Basic Server Configuration:**

**Document Root and Permissions:**

* NGINX involves defining the document root, which is the directory where website files like `index.html` are stored.
* Ensure proper permissions are set for the web server to access these files.

nginx

```
server {
    listen 80;
    server_name example.com;
    root /var/www/html;
    index index.html;
}
```

bash

```
$ sudo chown -R nginx:nginx /var/www/html
```

**Managing `index.html` and 404 Error Pages:**

nginx

```
index index.html;
```

* Configure a custom 404 error page as follows:

nginx

```
error_page 404 /404.html;
location = /404.html {
    root /var/www/html;
}
```

*   Test static website:

    bash

    ```
    curl domain_name_or_ip
    ```

**Virtual Hosts (Server Blocks):**

* Allow nginx to host multiple websites on the same server.
* Each server block depends on the same IP + port. It will be based on domain name.
* Named based virtual hosting.

**Managing Virtual Hosts (Server Blocks):**

nginx

```
server {
    listen 80;
    server_name example.com;
    root /var/www/example;
    index index.html;
}

server {
    listen 80;
    server_name test.com;
    root /var/www/test;
    index index.html;
}
```

**Tips for Multiple Environments (Development vs. Production):**

*   In production environments, consider enabling caching, compression, and tighter security controls (e.g., SSL).

    nginx

    ```
    location / {
        proxy_cache my_cache;
        proxy_cache_valid 200 1h;
    }
    ```
*   In development, Enable detailed logging and easier access for debugging.

    nginx

    ```
    error_log /var/log/nginx/dev_error.log debug;
    ```

**Basic URL Rewriting and Redirection:**

*   The `rewrite` directive allows you to modify the URL before it's processed by the server.

    nginx

    ```
    rewrite ^/oldpage$ /newpage permanent;
    ```
*   In addition to `rewrite`, you can use the `return` directive for simpler redirects.

    nginx

    ```
    server {
        listen 80;
        server_name oldsite.com;
        return 301 http://newsite.com$request_uri;
    }
    ```
* **302 Redirect:** Used for temporary redirection.

**PRACTICAL EXAMPLE: CONFIGURING NGINX WITH ALL SECTIONS IN MODULE 2:**\
In this practical example, we will walk through the steps required to:

1. Set up NGINX with a basic configuration.
2. Serve static content from a website.
3. Configure multiple virtual hosts (server blocks) for different domains.
4. Set up custom error pages and directory listings.
5. Implement basic URL rewriting and redirection.

*   To test configuration:

    bash

    ```
    nginx -t
    ```

***

#### **Part 4: Security and SSL Configuration**

**What is SSL and TLS?**

* **Secure Sockets Layer (SSL):** A protocol developed in the mid-1990s by Netscape to secure data transmitted between a web server and a client.
* **Transport Layer Security (TLS):** The successor to SSL, providing stronger encryption and better security features.
* TLS is the current standard for encrypting web communications, but many still refer to it as SSL.

**Key Concepts of SSL/TLS:**

1. **Encryption:** SSL/TLS encrypts the data transferred between the client and the server, ensuring that any data intercepted by a third party is unreadable.
2. **Authentication:** SSL/TLS uses certificates to verify the identity of the server (and optionally the client), helping to protect against man-in-the-middle attacks.
3. **Integrity:** Data is not only encrypted but also signed, ensuring that it hasn’t been altered in transit. If someone tries to modify the data, the connection will be invalidated.
4. **Symmetric and Asymmetric Encryption:**
   * **Symmetric Encryption:** Both parties use the same key to encrypt and decrypt data. This method is fast but requires securely sharing the key.
   * **Asymmetric Encryption:** Two different keys are used – one for encryption (public key) and one for decryption (private key). This process is slower but is used during the initial handshake process of SSL/TLS to securely exchange keys.

**What is HTTPS?**

* **HyperText Transfer Protocol Secure:** The secure version of HTTP that uses SSL/TLS for encryption.
* **Role of HTTPS:**
  * Encrypts communications to prevent dropping and data theft.
  * Authenticates the website to ensure users are communicating with the legitimate server and not a malicious impostor.

**Why is SSL/TLS and HTTPS Important?**

* **Data Protection:** SSL/TLS encryption ensures that sensitive data, such as login credentials, credit card numbers, and personal information, are transmitted securely and can’t be intercepted by attackers.
  * ✖ In an unsecured connection (HTTP), any data sent from the client to the server is in plain text and can easily be intercepted by hackers using packet-sniffing tools.
* **Authentication and Trust:** HTTPS uses SSL certificates to validate the authenticity of a website, assuring users that they are interacting with a legitimate website. This prevents man-in-the-middle (MITM) attacks.
* **Data Integrity:** SSL/TLS provides integrity checks that ensure the data transmitted between the server and the client has not been altered during transmission.
* **Compliance:** Many industries have strict compliance requirements (e.g., PCI-DSS for online payments) that mandate the use of HTTPS.
* **SEO and User Experience:** Search engines prioritize HTTPS-enabled sites. Modern browsers label HTTP sites as "Not Secure."

**How Does SSL/TLS Work? The SSL/TLS Handshake Process:**

* When a browser requests a page from an HTTPS site, the SSL/TLS handshake process begins.
* During this handshake, the browser and server agree on encryption protocols, authenticate using digital certificates, and exchange keys to create a secure, encrypted session.

**SSL/TLS Certificate Types:**

1. **DV (Domain Validated):** Basic certificate that only verifies domain ownership.
2. **OV (Organization Validated):** Provides more detailed organization information, validating that the website belongs to a legitimate entity.
3. **EV (Extended Validation):** Offers the highest level of trust and security, often displaying the organization name in the browser's address bar.

**Generating Self-Signed SSL Certificates using OpenSSL:**

1.  Install OpenSSL if it's not already installed.

    bash

    ```
    $ yum install openssl
    ```
2.  Generate a private key.

    bash

    ```
    $ openssl gemsa -out /etc/ssl/private/nginx-selfsigned.key 2048
    ```
3.  Create a self-signed certificate.

    bash

    ```
    $ openssl req -new -x509 -key /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt -days 365
    ```

**Configuring SSL in NGINX:**

1. Edit the NGINX server block to enable SSL.
2.  Configure the SSL certificate and key.

    nginx

    ```
    server {
        listen 443 ssl;
        server_name example.com;
        text
        ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
        ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
        root /var/www.example.com;
        index index.html;
    }
    ```

**Diffie-Hellman Parameters for Enhanced Security:**

1.  Generate strong Diffie-Hellman parameters.

    bash

    ```
    $ openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
    ```
2.  Add the Diffie-Hellman parameter file to your NGINX SSL configuration.

    nginx

    ```
    ssl_dhparam /etc/ssl/certs/dhparam.pem;
    ```

**Using Certbot to Obtain Free SSL Certificates from Let's Encrypt:**

1.  Install Certbot.

    bash

    ```
    $ yum install certbot python3-certbot-nginx
    ```
2.  Obtain a Let's Encrypt SSL certificate.

    bash

    ```
    $ certbot --nginx -d example.com -d www.example.com
    ```

    * Certbot will automatically configure SSL in NGINX, create the necessary certificate and key files, and reload NGINX.
    * Let's Encrypt certificates are valid for 90 days.
3. **Renewal:** Certbot can be set up with a cron job to handle certificate renewal automatically.
   *   Renew certificate manually (dry-run):

       bash

       ```
       $ certbot renew --dry-run
       ```

**Enforcing HTTPS:**

**Redirecting HTTP to HTTPS Globally or Per Site:**

nginx

```
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;
}
```

**Configuring HSTS (HTTP Strict Transport Security):**

nginx

```
server {
    listen 443 ssl;
    server_name example.com;
    text
    # SSL and key files
    ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
    ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;
    # Enforce HTTPS
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    root /var/www/example.com;
    index index.html;
}
```

**SSL Optimization:**

Disable older, insecure protocols like SSLv3 and weak ciphers.

nginx

```
ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers on;
ssl_ciphers 'HIGH: !aNULL:!MD5';
```

**Practical Example: Secure NGINX with SSL and Firewalls**

**Scenario Overview:**\
You are tasked with securing a website (`example.com`) with SSL using Let's Encrypt, enforcing HTTPS redirection, optimizing SSL settings, and configuring the firewall.

* Create ssl certificate through let's encrypt or self sign ssl certificate and also there is a lot of ways to create ssl.

**Example Configuration (`example.com.conf`):**

nginx

```
server {
    listen 86;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name example.com;
    text
    ssl_certificate /etc/ssl/certs/example.com.crt;
    ssl_certificate_key /etc/ssl/certs/example.com.key;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'HIGH:!aNULL:!MD5';
    root /var/www/example.com;
    index index.html;
}
```

**Check Firewall and Test Configuration:**

bash

```
root@nginx -js firewall-cmd --list-all
root@nginx -js nginx -t
root@nginx -js systemctl reload nginx
```

**Final Secure Configuration Summary:**

nginx

```
server {
    listen 443 ssl;
    server_name example.com;
    ssl_certificate /etc/nginx/ssl/example.com.crt;
    ssl_certificate_key /etc/nginx/ssl/example.com.key;
    location / {
        root /var/www/html;
        index index.html;
    }
}
server {
    listen 80;
    server_name example.com;
    return 301 https://$host$request_uri;
}
```

* `listen 443 ssl;` : Tells Nginx to listen on port 443 (the default port for HTTPS) with SSL enabled.
* `ssl_certificate` and `ssl_certificate_key` : Specifies the paths to your SSL certificate and private key.
* `return 301 https://$host$request_uri;` : Redirects all HTTP requests to HTTPS.

***

#### **Part 5: NGINX as a Reverse Proxy and Load Balancer**

**Understanding Reverse Proxy:**

* Reverse proxy: receive requests from client and forward to the backend servers and after the backend server process the request and send back to reverse proxy.
* **Understanding difference between reverse proxy and forward proxy:**
  * **Reverse proxy:** Works on behalf of the backend server.
  * **Forward proxy:** Works on behalf of the client to access the internet. Sits between the client and the internet.

**Key Benefits of Using a Reverse Proxy:**

1. **Load Balancing:** A reverse proxy can distribute incoming traffic across multiple backend servers, ensuring no single server is overwhelmed.
2. **Improved Security and Anonymity:** Reverse proxies help secure backend servers by hiding their IP addresses from the public. It decrypts encryption and this helps reduce work on the backend servers. When requests come using https, it decrypts them and passes them to the backend server using http. When it gets the response it does encryption again and sends the response to the client through https. This process improves performance and makes SSL certificate management centralized, so backend servers focus only on processing.
3. **SSL Termination (SSL Offloading):** Reverse proxies can handle SSL/TLS encryption and decryption, offloading this resource-intensive process from backend servers.
4. **Caching for Better Performance:** Reverse proxies can cache content from the backend servers, reducing the need for repetitive data fetching. (caching: Temporarily storing)
5. **Compression and Optimization:** Reverse proxies can optimize content by compressing responses from backend servers before forwarding them to clients.
6. **Centralized Authentication and Authorization:** Reverse proxies can be used to manage access control, such as requiring authentication for certain endpoints.
7. **Global Traffic Management and Redirection:** Reverse proxies can be configured to direct traffic to geographically distributed backend servers. Provide the best user experience and reduce latency and redirect the requests to the closest data center especially for big companies.
8. **Fault Tolerance and High Availability:** If one backend server fails, the reverse proxy can redirect traffic to other healthy servers without downtime.

**Configuring NGINX as a Reverse Proxy:**

NGINX can be easily configured as a reverse proxy using the `proxy_pass` directive. This forwards client requests to backend servers.

**Basic Setup:**

nginx

```
server {
    listen 80;
    server_name www.example.com;
    text
    location / {
        proxy_pass http://backend_server;
    }
}
```

**Setting Custom Headers:**

nginx

```
location / {
    proxy_pass http://backend_server;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

**Handling Timeouts:**

nginx

```
location / {
    proxy_pass http://backend_server;
    proxy_connect_timeout 60s;
    proxy_read_timeout 120s;
}
```

* `proxy_connect_timeout`: The maximum time Nginx will wait to establish a connection with the backend server.
* `proxy_read_timeout`: The maximum time Nginx will wait for a response from the backend server after the connection is established.

**Load Balancing:**

* **Definition:** Load balancing is an essential technique in web infrastructure, distribute the traffic on specific numbers of servers. That improve availability and app performance. It involves that no single server has a lot of load and handles many requests, so it improves server response speed and ensures continuous service availability.
* NGINX is a famous tool for load balancing because it's fast, flexible and easy configuration.
* NGINX support multiple ways for load balancing. Each way has use cases.

**Load Balancing Methods:**

1.  **Round Robin (Default):**

    * Distribute requests equally and in order.
    * Works by distributing incoming client requests evenly across all available backend servers in a cyclical manner.
    * Doesn't consider the current load or response times.
    * Example: 3 backend servers having the same capabilities.

    nginx

    ```
    upstream backend_servers {
        server backend1.example.com;
        server backend2.example.com;
        server backend3.example.com;
    }
    server {
        location / {
            proxy_pass http://backend_servers;
        }
    }
    ```
2.  **Least Connections:**

    * Sends traffic to the backend server with the fewest active connections at the moment of the request.
    * More dynamic than round robin, as it accounts for real-time traffic and connection load.
    * Ideal for environments where request durations vary or when backend servers have different capacities.

    nginx

    ```
    upstream backend_servers {
        least_conn;
        server backend1.example.com;
        server backend2.example.com;
    }
    server {
        location / {
            proxy_pass http://backend_servers;
        }
    }
    ```
3.  **IP Hash:**

    * Useful for applications that require sessions to remain continuous. The same client must deal with the same backend server it dealt with from the beginning of the session until its end.
    * NGINX creates a hash on the client IP and this hash specifies which backend server will deal with the request, so that as long as the client uses the same IP, it will be served by the same backend server.
    * Ensures that requests from the same client IP address are consistently routed to the same backend server.
    * Advantages: Guarantees session persistence without requiring complex state management.

    nginx

    ```
    upstream backend_servers {
        ip_hash;
        server backend1.example.com;
        server backend2.example.com;
    }
    server {
        location / {
            proxy_pass http://backend_servers;
        }
    }
    ```
4.  **Weighted Load Balancing:**

    * Servers that have a larger weight will receive a larger share of requests.
    * This method is ideal for environments where backend servers have different hardware and performance.
    * Each server has a specific weight and the server that has a big weight means that server will have a larger number of requests relative to other servers.
    * The default weight for the servers will be one. If servers have more resources than others, give them more weight. If you don't do configuration for the weights, they are equal.
    * Advantages: Helps efficiently utilize more powerful servers by directing more traffic to them.

    nginx

    ```
    upstream backend_servers {
        server backend1.example.com weight=3;
        server backend2.example.com;
    }
    server {
        location / {
            proxy_pass http://backend_servers;
        }
    }
    ```

    * `weight=3`: This server will accept three times the traffic compared to backend server two.
5.  **Least Time (available in NGINX Plus):**

    * The server with a low number of connections and an average low response time is the one that will receive the request.
    * NGINX monitors load and response time for each backend server and the servers who reply first and have fewer number of connections it will be chosen for the coming request.
    * Takes into account not only the number of active connections but also the response time of each server.
    * Advantages: Maximizes performance by taking response time into account.

    nginx

    ```
    upstream backend_servers {
        least_time header;
        server backend1.example.com;
        server backend2.example.com;
    }
    server {
        location / {
            proxy_pass http://backend_servers;
        }
    }
    ```

**Health Checks:**

* Health checks are an essential part of load balancing in NGINX. They ensure that traffic is only sent to healthy backend servers.
* **Passive health checks:** NGINX marks a server as down when it stops responding or returns an error status code (such as 500).
* **Active health checks (NGINX Plus):** NGINX sends regular requests to backend servers to verify they are working properly.
* **Fallback Configuration Example:**

nginx

```
upstream backend_servers {
    server backend1.example.com;
    server backend2.example.com;
}
server {
    location / {
        proxy_pass http://backend_servers;
        error_page 502 503 = @fallback;
    }
    location @fallback {
        return 200 "The backend is currently unavailable.";
    }
}
```

**Caching with NGINX:**

* NGINX can cache responses from backend servers to improve performance for repeated requests by using the `proxy_cache` and `proxy_cache_valid` directives.
* **Basic Example:**

nginx

```
proxy_cache_path /data/nginx/cache keys_zone=my_cache:10m;
server {
    location / {
        proxy_cache my_cache;
        proxy_pass http://backend.server;
    }
}
```

* **Detailed Example:**

nginx

```
proxy_cache_path /data/nginx/cache levels=1:2 keys_zone=my_cache:10m max_size=1g;
location / {
    proxy_cache my_cache;
    proxy_cache_valid 200 302 10m;
    proxy_cache_valid 404 1m;
}
```

**Practical Example: Load Balancing & Reverse Proxy**

**Scenario Overview:**\
We have two backend servers (`backend1.example.com` and `backend2.example.com`) that serve a website. We will configure NGINX to act as a reverse proxy, load balance the requests using the round robin method, enable caching for static assets, and ensure traffic is secure using HTTPS.

***

#### **Part 6: Advanced Topics and Performance Optimization**

**Tuning Worker Processes and Connections for High Traffic:**

* The number of worker processes should usually match the number of CPU cores on your system.
* `worker_processes`: if you don’t know cpu cores. Set its value `auto` so automatically nginx will specify it based on cpu cores on the server.

nginx

```
worker_processes auto; # Automatically set based on available CPU cores
events {
    worker_connections 4096; # Handle more concurrent connections
}
```

**Optimizing Buffer Sizes:**

* Buffers control how much data NGINX can handle in memory before writing it to disk or processing it.
* Setting these values correctly helps client connection management effectively.
* Helps prevent memory from handling very large files.

nginx

```
client_body_buffer_size 16k;
client_max_body_size 2M; # Restrict large file uploads
```

**Managing Timeouts:**

* Timeouts determine how long NGINX waits for certain events like receiving headers or completing a request.

nginx

```
keepalive_timeout 65;
client_header_timeout 10s;
```

**Improving File Serving Performance:**

* These directives optimize how NGINX serves static files. `sendfile` minimizes CPU usage, while `tcp_nopush` reduces the number of packets sent.

nginx

```
sendfile on;
tcp_nopush on;
tcp_nodelay on;
```

**Rate Limiting and Traffic Shaping:**

* The goal here is to protect against attacks on the nginx server like DDoS attack and controlling the number of requests that a client could do.
*   **Rate Limiting Example:** Here in this example client does one request per second. Each IP can do 10 connections at the same time. Here we are controlling the number of requests that come to the server.

    nginx

    ```
    limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
    limit_conn_zone $binary_remote_addr zone=addr:10m;
    server {
        location /login {
            limit_req zone=one burst=5 nodelay;
            limit_conn addr 10;
        }
    }
    ```
*   **Bandwidth Throttling (Speed Limiting):**

    * Server manages load by specifying the speed of data transferring for specific points.
    * Useful in cases where the user tries to download a large file and consumes the server resources excessively. Specify download speed for the clients.
    * Prevent flooding of login pages by bots and protect API endpoints from being overwhelmed by high traffic.
    * You can limit how many concurrent connections a single IP address can establish, thus preventing abuse by a single user or attacker.
    * Bandwidth throttling helps manage server load by limiting the data transfer speed for specific locations.

    nginx

    ```
    limit_rate 10k;
    ```

**Logging and Monitoring:**

* NGINX logs each processing request and records problems as well and logs are too important to understand traffic pattern in order to face any problem we face.
* You must take into account that you manage logs so they do not consume an excessive amount of server space.
* Archive logs and delete old logs to save storage by doing log rotate configuration for nginx.
*   **Log Rotate Configuration Example (`/etc/logrotate.d/nginx`):**

    text

    ```
    /var/log/nginx/*.log {
        daily
        rotate 14
        compress
        missingok
        notifempty
        create 640 nginx adm
        sharedscripts
        postrotate
            if [ -f /var/run/nginx.pid ]; then
                kill -USR1 `cat /var/run/nginx.pid`
            fi
        endscript
    }
    ```
*   **Monitoring Commands:**

    * Monitor nginx server performance in real time operation through `ngxtop`.
    * Monitor connection activities and network activities on our server.

    bash

    ```
    netstat -tnlp | grep nginx
    ```

    * The first command (`ngxtop`) provides a real-time view of performance based on access logs analytics.
    * The second command (`netstat`) monitors connection activities and network activities on our server.
