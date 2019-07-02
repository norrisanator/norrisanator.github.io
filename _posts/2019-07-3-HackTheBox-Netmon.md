---
layout: post
title: Hack The Box Netmon
---
Brief:
This was my first hack the box, for a much better guide see https://www.youtube.com/watch?v=ZxvgniJXbOo.

nmap -sV -sC -oA nmap 10.10.10.152

Nmap scan report for 10.10.10.152
Host is up (0.23s latency).
Not shown: 954 closed ports, 41 filtered ports
PORT    STATE SERVICE    VERSION
21/tcp  open  tcpwrapped
80/tcp  open  tcpwrapped
135/tcp open  tcpwrapped
139/tcp open  tcpwrapped
445/tcp open  tcpwrapped

Checking out 10.10.10.152 in the browser brings us to a PRTG network monitor login.

I then connected to the ftp port 21 with ncftp 10.10.10.152 and explored around and found the user.txt in the user/public folder.

cat user.txt to retrieve the hash.

Searching PRTG in google reveals that paessler is  the host website and we have a folder called paessler.
In there we find backup files, two were encrypted but one of the old ones wasn't.
configuation.old.bak contained the user prtgadmin and the password PrTg@dmin2018.
Trying to use those credentials to log in failed, after a lot of thinking I tried Prtg@min2019 as the password and was able to login.

The software running was PRTG version 18.1.37.13946 and that version had flaws.
https://packetstormsecurity.com/files/148334/PRTG-Command-Injection.html
https://thehackingtutorials.com/prtg-network-monitor-exploit-with-poc/
https://www.codewatch.org/blog/?p=453

If you navigate to set up account settings and click on notifcations. Then add a notifcation it allows you to execute code.
Select execute program.
 
test.txt; tree /f c:\Users\Administrator > c:\output.txt
 
This command gives us the folder structure of the folder we couldn't access via the ftp and in it we see the root.txt

test.txt; more c:\Users/Administrator\Desktop\root.txt > c:\output.txt

Now this command more allows you to via files and then it writes it to output.txt

Things I learnt from this box.

- Research things you find and you will gain a better understanding of how they work.
- How nmap works
- How to connect via ftp
- How to execute code remotely
- How to browse and edit files with linux

Coming soon Luke writeup
