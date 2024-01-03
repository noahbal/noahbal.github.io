---
title : Open Redirect
date : 2024-01-03 14:52:00 +0100
categories : [Root-me, Web Server]
tags : [open redirect, hash, cryptography, security breach] # TAGS names should always be lowercase
---

## Resources of the challenge

The statement of this challenge is : 
"Find a way to make a redirection to a domain other than those showed on the web page."

The web page shows 3 buttons that direct the user to Facebook, Twitter or Slack
![intro](/img/open-redirect/page-openredirect.png)

The documentation is perfect for this challenge, it is an article on the Open Redirect flaw that is simple and clear.
I repost it [here](/img/open-redirect/Open_Redirect.pdf).

## Starting resolution

Once that I knew which flaw to exploit, I took a look to the source code. Here's how the redirect links to social networks are written :
```html
<a href='?url=https://facebook.com&h=a023cfbf5f1c39bdf8407f28b60cd134'>facebook</a>
<a href='?url=https://twitter.com&h=be8b09f7f1f66235a9c91986952483f0'>twitter</a>
<a href='?url=https://slack.com&h=e52dc719664ead63be3d5066c135b6da'>slack</a>
```

The **?** stands before the parameters of a URL so I understood that I needed to modify the URL by setting the parameters of another site to redirect the page.

First, I naively tried : **?url=https://youtube.com**

It didn't work, so I realized there was a second parameter to include in the URL, the **h**.

I therefore tried to copy an **h** from another link, such as the one on Facebook : **?url=https://youtube.com&h=a023cfbf5f1c39bdf8407f28b60cd134**

The page reloaded with the message: **Incorrect hash!**

The forum confirmed that I should create my own hash by understanding the link between the domain name and its hash. Logically, I did some research into hash functions.

## Hash

Hashing is a mathematical technique that transforms data of variable size into a fixed value of specific length, usually a sequence of hexadecimal characters. This fixed-length value is called a *hash*.

Hashing is designed to be a one-way function, meaning that it's easy to create a hash from the original data, but extremely difficult (if not impossible) to retrieve the original data from the hash. 

Hashes are commonly used in IT security to store passwords securely. When the user attempts to log in, the system hashes the password entered by the user and compares it with the hash stored in the database. If the two hashes match, this means the password is correct, but the system never needs to store the actual password.

Common hash algorithms include MD5, SHA-1, SHA-256 and SHA-512.

### MD5 (Message Digest Algorithm 5)

MD5 is a cryptographic hash algorithm that takes variable-size data as input and produces a 128-bit (16-byte) hash fingerprint, usually represented as a **32-character hexadecimal string**.

The MD5 algorithm is designed to be fast and efficient, making it popular in the past for hashing and data integrity verification applications. However, over the years, vulnerabilities have been discovered in MD5, making it a less secure choice for applications requiring high security.

The main vulnerabilities in MD5 are related to **collisions**. Indeed, it is possible to find two different datasets that produce the same MD5 hash. This makes MD5 vulnerable to "collision" attacks, where an attacker can intentionally create different datasets with the same hash, which can compromise the security of systems that rely on MD5 to store passwords or check file integrity.

### SHA (Secure Hash Algorithm)

SHA hash algorithms are considered more secure than MD5 due to their more robust design and greater resistance to cryptographic attacks. Here are a few reasons why SHA is considered more secure:

1. **Longer output length**: SHAs produce hash of greater length than MD5. For example, SHA-256 produces a 256-bit (32-byte) hash, while SHA-512 produces a 512-bit (64-byte) hash. A longer hash length provides a larger search space for attackers, making collisions much harder to find.
2. **Collision resistance** : SHA algorithms have been designed to resist collision attacks, where an attacker tries to find two different datasets that produce the same hash.
3. **Resistance to pre-images** : SHA algorithms are also resistant to preimage attacks, where an attacker tries to recover the original data from a given hash. This property is essential to ensure that hashes cannot be "dehashed" to recover the original data.
4. **Standardization and cryptographic evaluation** : The security community regularly performs rigorous cryptographic evaluation of the SHA algorithms. Moreover, they are widely standardized in international standards. The ongoing standardization and revision process guarantees their quality and resistance to new cryptographic attacks.

## End of the resolution

After counting the number of characters in the challenge hash (32), I realized that it was the result of one of these algorithms.

After some research, I found the link between the URL and its hash :

**a023cfbf5f1c39bdf8407f28b60cd134** is the MD5 hash of **https://facebook.com**

Ultimately, I calculated the MD5 hash of **https://youtube.com**, then used Burp proxy to display a page with the password just before the redirection to YouTube.