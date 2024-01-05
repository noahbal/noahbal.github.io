---
title : Command Injection
date : 2024-01-04 10:48:00 +0100
categories : [Root-me, Web Server]
tags : [php, security breach, bash] # TAGS names should always be lowercase
---

## Context

To begin this challenge, no document was available. I started by doing some quick research on the Internet. Command injection is a flaw similar to XSS, where the content of user responses is not filtered, allowing code to be injected and executed **on the server side**.

It took me a while to understand the service offered by the web page : you enter an IP address and the page pings you back at that address. After consulting the forum, I discovered that for the injection to work, the code had to be preceded by an IP address like this :

![test](/img/command-injection/ping.png)
*The IP 127.0.0.1 points back to the computer making the request*

The only indication in the challenge was :

"Find a vulnerabilty in this service and exploit it.
The flag is on the index.php file."

## Resolution

I consulted the **source code** to see that the `index.php`{: .filepath} file was stored on the web page. So I looked for a way to access the source code contained in the file.

Further research in the forum gave me a clue : command injection is not necessarily a PHP command. Back to searching the Internet, I found that requests in `Bash` were possible Knowing the basics of Bash, I figured there had to be a command to read the contents of a file. 

In fact, there are 2 : **`cat`** is used to quickly display the complete contents of one or more files, while **`less`** is used to display the contents in a paginated fashion, allowing more convenient navigation through large files.

The query allowed me to access the code by looking in the page's source code :
```bash
127.0.0.1; less index.php
```

Here is the content of the `index.php`{: .filepath} file :
```php
<?php
$flag = "".file_get_contents(".passwd")."";
if(isset($_POST["ip"]) && !empty($_POST["ip"])){
$response = shell_exec("timeout -k 5 5 bash -c 'ping -c 3 ".$_POST["ip"]."'");
echo $response;
}
?>
```

I used  **`cat`** to display the content of the `.passwd`{: .filepath} file and I found the password !