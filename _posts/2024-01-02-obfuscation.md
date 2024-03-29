---
title : Obfuscation
date : 2024-01-02 11:39:00 +0100
categories : [Root-me, Web Client]
tags : [javascript] # TAGS names should always be lowercase
---

## Context

This challenge takes place in the source code of a web page :

```html
<html>
<head>
    <title>Obfuscation JS</title>
    <script type="text/javascript">
    function dechiffre(pass_enc){
        var pass = "70,65,85,88,32,80,65,83,83,87,79,82,68,32,72,65,72,65";
        var tab  = pass_enc.split(',');
                var tab2 = pass.split(',');var i,j,k,l=0,m,n,o,p = "";i = 0;j = tab.length;
                        k = j + (l) + (n=0);
                        n = tab2.length;
                        for(i = (o=0); i < (k = j = n); i++ ){o = tab[i-l];p += String.fromCharCode((o = tab2[i]));
                                if(i == 5)break;}
                        for(i = (o=0); i < (k = j = n); i++ ){
                        o = tab[i-l]; 
                                if(i > 5 && i < k-1)
                                        p += String.fromCharCode((o = tab2[i]));
                        }
        p += String.fromCharCode(tab2[17]);
        pass = p;return pass;
    }
    String["fromCharCode"](dechiffre("\x35\x35\x2c\x35\x36\x2c\x35\x34\x2c\x37\x39\x2c\x31\x31\x35\x2c\x36\x39\x2c\x31\x31\x34\x2c\x31\x31\x36\x2c\x31\x30\x37\x2c\x34\x39\x2c\x35\x30"));
    
    h = window.prompt('Entrez le mot de passe / Enter password');
    alert( dechiffre(h) );
    
</script>
</head>

</html>
```

We can see a call to a function named **dechiffre** : 
```javascript
String["fromCharCode"](dechiffre("\x35\x35\x2c\x35\x36\x2c\x35\x34\x2c\x37\x39\x2c\x31\x31\x35\x2c\x36\x39\x2c\x31\x31\x34\x2c\x31\x31\x36\x2c\x31\x30\x37\x2c\x34\x39\x2c\x35\x30"));
```

## Resolution

I decrypted the string in argument using the command :
```javascript
unescape("\x35\x35\x2c\x35\x36\x2c\x35\x34\x2c\x37\x39\x2c\x31\x31\x35\x2c\x36\x39\x2c\x31\x31\x34\x2c\x31\x31\x36\x2c\x31\x30\x37\x2c\x34\x39\x2c\x35\x30");
```

Then, I got a Unicode encoded string : *55,56,54,79,115,69,114,116,107,49,50*

> **Unicode** is a text encoding standard designed to support the use of text written in all of the world's major writing systems. Version 15.1 of the standard defines 149 813 characters and 161 scripts used in various ordinary, literary, academic, and technical contexts. It has inherited **ASCII** standard in its 128 first code points.
![Unicode Table](https://devblogs.microsoft.com/wp-content/uploads/sites/33/2019/02/command-line-backgrounder-ascii-600x393.png)
*Unicode Table* 
Sources : [Wikipedia](https://fr.wikipedia.org/wiki/Unicode), [Microsoft](https://devblogs.microsoft.com/commandline/windows-command-line-unicode-and-utf-8-output-text-buffer/) 
{: .prompt-tip }

Using the Javascript command : 
```javascript
String.fromCharCode(55,56,54,79,115,69,114,116,107,49,50)
```

I finally got the password ! 