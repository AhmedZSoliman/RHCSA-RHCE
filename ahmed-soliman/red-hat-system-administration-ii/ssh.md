# SSH

* **Used to connect to other machines remotely and securely to run any commands you want.**
* **Remote connections must be secured to avoid the risk of being hacked.**

***

* **SSH   is a daemon service.**

***

### The first way: SSH using **password authentication** to connect to any server

* **This way is less secure than key authentication.**
* **SSH uses both asymmetric and symmetric cryptography.**
* **The server sends its public host key to the client.**
* **The client saves this public key to verify the server identity.**
* **The public key is NOT the key used to encrypt the data.**
* **The client and the server use a key exchange (like Diffie–Hellman) to create a shared symmetric session key.**
* **This symmetric session key is used to encrypt and decrypt all data between client and server.**
* **So the client and the server share the SAME symmetric session key for the connection.**
* **All machines can get the server public key (public key for identity verification, not for data encryption).**

### To see all public keys stored on your machine for servers you connected to:

```
~/.ssh/known_hosts
```

#### Each entry contains:

```
IP   hostname   key type   remote server public key
```

***

### To know the public key configuration of the remote server:

```
/etc/ssh/sshd_config
```

#### We have types of keys like:

```
RSA, DSA, ECDSA
```

***

* #### The second way: SSH **Key-pair authentication**
  * **Uses asymmetric cryptography.**
  * **More secure than password authentication.**
  * **You can access the server without typing a password.**
  * **Authentication is done using a key, not a password.**
  * **The client generates two keys, not the server:**
    * One **public key**
    * One **private key**
  * **The client sends the public key to the server for the user account (the privileges you want to access).**
  * **The private key stays on your local machine and must never be shared.**
  * ✅ **The public key is NOT used to encrypt normal data packets.**
  * ✅ **The server uses the public key only to verify your identity.**
  * ✅ **The client proves it has the private key by signing a challenge from the server.**
  * ✅ **If the signature is correct, the server allows login without a password.**

***

#### **Configuring Key-Pair with SSH**

*   **1- On the client: To generate key pair (public + private key)**

    ```
    ssh-keygen
    ```

    **To choose the key name and its location:**

    ```
    ssh-keygen -f /path/name
    ```
* **Note: When you create a key pair, if you want to protect it, put a passphrase on it.**\
  **When you connect to the remote server using the key pair, your machine will ask for the key password.**

***

*   **2- On the client: Send the public key (encrypted by the private key) to the remote server**

    ```
    ssh-copy-id -i <path of generated key> <user>@remotehost
    ```

    **Example:**

    ```
    ssh-copy-id -i dolfinkey.pub root@50.0.6.5
    ```

    **Example to send the public key to another server by default:**

    ```
    ssh-copy-id root@50.0.6.5
    ```

***

*   **3- On the client: If you want to access (connect to) the remote server by key**

    ```
    ssh -i <path of generated key> <user>@remotehost
    ```

    **Example:**

    ```
    ssh -i dolfinkey.pub root@50.0.6.5
    ```

    **Note: After that, connect without the key and it will not ask for the password:**

    ```
    ssh root@ip_or_hostname
    ```

***

*   **ssh-add is used to save the passphrase-supported key pair in memory if you do not want it to ask you for the password anymore.**\
    **It works like a browser saving passwords.**

    ```
    ssh-add key_path
    ```
