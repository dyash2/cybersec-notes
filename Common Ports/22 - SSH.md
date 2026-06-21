The Secure Shell (SSH) protocol is a method for securely sending commands to a computer over an unsecured network.
SSH uses cryptography to authenticate and encrypt connection between devices.

## **What is a Shell?**

A shell is a program that lets you perform operating system commands on a device, typically through a command-line interface (CLI).

#### **Understanding Secure Shell**

SSH was developed to replace Telnet, which functions much like SSH but without providing a secure environment. SSH adds various features to overcome Telnet’s limitations by allowing you to connect securely to a remote device over an unsecured network.

To use SSH, enter the following command from a terminal or a command prompt:

`ssh [username]@[host]`

username is the username on the remote host that you want to logon as, and host is the hostname or IP address of the remote device.

#### **How to Make Sure TCP Port 22 is Secure**

Hackers often target port 22 because they could potentially do unlimited damage if they can log onto a remote device. Because of the risk, many organizations will turn off port 22 and instead use a random port for SSH, somewhere between 1024 and 65535.

Understanding how to keep port 22 secure is critical for keeping your networks safe from intruders. Here are a few ways to mitigate the risks of port 22.

##### **1. Learn to Spot Misconfigurations and Weak Authentication**

Often, default SSH server implementations will allow logins as the root user. Because root has unlimited control over the remote device, you should disable these types of logins.

Another common SSH-related misconfiguration is when you only want to allow SFTP without shell access. To properly do this, you must disable TTY access, as well as port forwarding and tunneling. 

You also have to ensure that you set up security policies within your organization that prevent the use of weak credentials. There are several ways to make sure members of your organization are creating [more secure passwords](https://www.cbtnuggets.com/blog/certifications/security/4-things-everyone-does-wrong-with-passwords), and can help end users stay safe.

##### **2. Block Vulnerable Ports Like Port 22 on Your Firewall**

Because port 22 is so notorious for risks, many opt to block it from use altogether. Once you've enabled SSH tunneling, you can block port 22 in your firewall.

### **Exploitation Scenarios**

An SSH server can be exploited in different ways; here are the common scenarios that you should be looking for

> - Credentials brute-force 
> 
> - Appending a public key to the authorized_keys file on the server.
> 
> - SSH can be used to pivot to another host on the network. This can be achieved if one host is compromised and the attacked has access to the public and private keys on the victim's host (pivoting is a post-exploitation task.)
> 
> - Find a public exploit associated with the target Telnet server version.
> 
> - If the attacker can read the authorized_keys file of a DSA (not RSA) algorithm, then the attacker can use the public generated private keys and try to match it to the public key inside the authorized_keys file. (You will need a remote shell first or to read the file using the “local file inclusion” vulnerability of a web application. Once the attacker knows the private key associated with that public key, then the attacker can use the following command:

```
ssh -i [private key file] [user@ftp_server_ip]
```


#### **Nmap NSE Scan**
```
nmap ‐sV ‐O ‐sC ‐p22 ‐T5 <IP>
```

```
nmap ‐sV ‐O ‐‐script=ssh* ‐p22 ‐T5 <IP>
```

### Brute Force

Hydra
```
hydra -t 10 -L <User List> -P <Password List> ssh://<IP>
```

- `-t 10` → Runs 10 parallel connections (threads).
- `-L users.txt` → Username wordlist.
- `-P passwords.txt` → Password wordlist.
- `ssh://<IP>` → Target SSH service.

```
hydra -t 10 -e nsr -L <User List> -P <Password List> ssh://<IP>
```

- `-e nsr` → Tries additional password combinations:
    - `n` → Null/empty password.
    - `s` → Username as password.
        - Example: `admin:admin`
    - `r` → Reverse username as password.
        - Example: `root:toor`

#### Metasploit

_**Username Enumeration:**_

```
msfconsole

use auxiliary/scanner/ssh/ssh_enumusers

set RHOSTS <IP>

set USER_FILE <path to wordlist>

set RPORT 22

set THREADS 25

run
```