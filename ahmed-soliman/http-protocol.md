# Http Protocol

**What is an API?**

* **It is a layer of communication.**
* **It is a URL.**
* **Purpose:** It allows a system or user to request data from another system by sending specific parameters in code.
* **Integration:** An API is a way for a user to talk to a system or for one system to talk to another system.
* **The systems do not know each other’s backend.** They don’t know how the other system works internally to get the data they want.
* **The two systems don’t have to be written in the same programming language.**
* **The user or system doesn’t need to know what happens in the backend.**
* **The only connection between the user/system and the other system is calling the endpoint.**
* **You can call an endpoint like saying, “This system, give me data.”**
* **But you don’t know what happens in the background.** There could be many other systems involved.
* **The API gives you the data without needing to know what happens in the backend.**
* **For more information** [**https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/#h-versioning-our-apis**](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/#h-versioning-our-apis)

**HTTP Protocol Introduction**

* HTTP is used to send data between two machines easily.
* It is a text-based protocol.
* There is a place that stores data like text, images, or videos. This place can send data to anything, **but it must follow a specific format**.

**Origin Server:**

* It can be anything, even your personal mobile phone. There are no special requirements; any device that understands the protocol can work as a server.
* Sending requests to an origin server usually needs a fixed IP address, because device IPs can change frequently.
* The origin server is a computer with enough specifications to handle the expected number of requests. It usually has a fixed IP or a domain name pointing to it.

**User Agent:**

* This can be any device or software, such as a web browser, a command line terminal, or other tools.

<figure><img src=".gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

* Between the user agent and the origin server, there can be many devices and countries. There is no fixed number.
* “Connections” do not mean exactly what you might imagine.
* HTTP does not know your address, does not store it, and does not maintain a connection. Another protocol keeps your address just to reply to you.
* **URL:** We use links to reach any place on the internet. The link is called a URL.
* **URI:** It is an ID for a resource (anything: a book, a website, a person, an ID number, or a company).
  * It is an identifier for any resource. You can take it and use it anywhere. It is a standard made for identifying things.
* HTTP uses URIs for the resources you want to work with.
* Think of this as the general idea.
* URL is one practical application of this idea.

<figure><img src=".gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

* For communication, devices need an IP address and a domain name. The domain name is translated into an IP address.
* To specify **which program** you are talking to on a device, we use a **port**.
* On the same device, many programs can run at the same time. Each program has its own port number to communicate with the outside world. This way, incoming data goes to the correct program.
* If an application runs on a server and receives requests, it must request a port number from the operating system. If the port is already used, it will return an error.
* **Port:** It is an unsigned 16-bit integer (0–65535). These are all the ports you can use on the operating system.
* A port is not physical. As I said before, HTTP does not manage connections. Another protocol like TCP/IP handles this process. Think of it as a delivery system that takes the request and delivers it.
* There are **well-known ports** for popular services.
  * For example, most HTTP servers try to avoid the first 2000 ports and use higher numbers instead. All other ports are generally available.
  * Some services, like databases, have reserved port numbers.
  * Example: MySQL database service uses port **3306**.

<figure><img src=".gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

* The **user agent** or **HTTP client** makes HTTP requests.
* It should ask for the **domain** and **port** together.
* If the user agent does not identify a port number, the **default port** will be used.
  * You never type the port name after the website address in normal browsing.
* **Authority part** of a URL is followed by the **path**.
  * This part specifies the resource you need on the website or server.
* After the path, there is a **query**.
  * This is where we write parameters and their values.
  * You will see this part in many links on the internet.
  * If there are multiple parameters, separate them using **&**.
  * Search parameters are usually sent from the front-end through the query string.
* **Fragment** points to a secondary part inside the primary resource.
  * It is a part of the resource, especially if the resource has multiple parts.
  * Example: An HTML page is a resource and can have multiple sections.
  * HTTP can transfer different types of data, not just web pages.
  * If you want the browser to stop at a specific HTML element inside a page, you write that element’s ID as the fragment.

<figure><img src=".gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

* Using the **URL**, a programmer writes code to work with each part of the URL.
* You will write code that interacts with the **URL** directly.

**Body or Payload:**

* Some requests have a body, and some do not.

**HTTP Methods (Verbs):**

* **GET:** Returns a resource. No body is sent.
* **POST:** Sends a resource to the server. The server stores it based on the data sent in the body.
* **PUT:** Updates a resource. The server replaces the existing data with the data sent in the body.
* **DELETE:** Deletes a resource.

**Notes:**

* In reality, POST and GET were used in the past for anything—creating, updating, or deleting resources—but now we use the correct method for the correct purpose.
* GET is sent without a body.
* POST sends a payload in the body.
* You can use the resource path, query string, or body to identify the request details.
* The HTTP protocol expects each method to be used in the correct way.

<figure><img src=".gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

* **Path:** Part of the URL. It is the resource you are requesting.
* **Version:** The protocol name and its version (e.g., HTTP/1.1).
* **Headers:** Lines in the request that have **keys** and **values**.

Headers are classified into groups:

1. **Request control** – controls the request behavior.
2. **Conditions** – sets conditions for the request.
3. **Content-related** – information about the characters or language in the request.
4. **Authentication** – related to security and login.

* **User-Agent:** A header from the context group. It contains information about the client making the request.
  * Example: `curl` is a command-line tool that sends HTTP requests.
  * If you create your own HTTP client, it will also have a User-Agent header.
* **Host:** A mandatory header because it specifies the domain name.
* **Accept-Language:** Tells the server which languages the client understands.
  * Example: The client can say “I understand Arabic and English.”
  * The server **may** send the response in either language, but it is not required.
* **Content-Type:** Used when the request contains a body. It tells the server the type of data in the body.

**Important notes:**

* Even if the request is made correctly, the server is **not forced** to respond in a specific way.
* HTTP request specifications are mostly **recommendations**, and only a few parts are mandatory.

<figure><img src=".gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

* **Response:**
  * Like a request, a response has **headers** and a **body**.
  * The **headers** and **body** are separated by an empty line.
* **Response Status Code:**
  * These are numbers agreed upon to represent different types of responses.
  * The numbers are divided into **hundreds**:
    1. **100s:** Informational responses.
    2. **200s:** Success – the request was successful, but not all successes are the same.
    3. **300s:** Redirection – the request is redirected to another server or location. Different types of redirects exist.



<figure><img src=".gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

**Most Common HTTP Status Codes:**

* **200 OK:** The request was successful. The server returned the resource you asked for.
* **404 Not Found:** The requested resource does not exist on the server. You usually see this when you try to open a page that is missing.
* **500 Internal Server Error:** Something went wrong on the server while processing the request. The server failed to handle the request properly.



