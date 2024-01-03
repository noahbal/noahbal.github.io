---
title : IP Restriction Bypass
date : 2024-01-03 11:17:00 +0100
categories : [Root-me, Web Server]
tags : [http, ip spoofing, rfc 1918, proxy] # TAGS names should always be lowercase
---

## Resources for the challenge

### Introduction

The challenge starts with this statement : 

"Dear colleagues, we’re now managing connections to the intranet using private IP addresses, so it’s no longer necessary to login with a username / password when you are already connected to the internal company network.

Regards,

The network admin"

The web page looks like this : 
![intro](/img/ip-bypass/intro.png)
*My IP adress must belong to the LAN in order to pass this login page*

### Request for Comments 1918

**RFC 1918**, also known as "Request for Comments 1918", is a technical specification published by the Internet Engineering Task Force (IETF) in 1996. This RFC defines private IP address ranges for use within local area networks (LANs) that are not routable over the public Internet.

These are the private IP address ranges defined in RFC 1918 :

* 10.0.0.0 to 10.255.255.255
* 172.16.0.0 to 172.31.255.255
* 192.168.0.0 to 192.168.255.255

These private IP address ranges have been reserved for use within private networks, such as home networks, corporate networks or local area networks (LANs). IP addresses in these ranges can be assigned to devices connected to these networks without conflict with routable IP addresses on the public Internet.

The use of private IP addresses enables LANs to operate internally without requiring a unique public IP address for each device. Routers and DHCP (Dynamic Host Configuration Protocol) servers are generally used to assign private IP addresses to devices connected to the local network.

It's important to note that private IP addresses are not unique across the Internet. When devices connected to a local network need to communicate with devices on the Internet, Network Address Translation (NAT) is often used to convert private IP addresses into routable public IP addresses on the Internet.

RFC 1918 is widely used to ensure that local networks can use IP addresses without conflict with routable IP addresses on the Internet.

This document is really useful for the challenge but it only gives us part of the solution. We need to connect to an intranet, using a private IP. The question is : how to change my IP adress ?

## HTTP request and proxy

The Root-Me forum gave me a crucial clue : HTTP headings. I needed to intercept **HTTP requests**, and potentially modify them in order to change my IP address. The forum directed me to the ideal tool for that job : a proxy.

> A **proxy** is a server that acts as an intermediary between a client and an end server. It allows the client to send requests through it, then forwards these requests to the end server. The proxy then receives the server's responses and sends them back to the client. Proxies can be used for a variety of reasons, such as anonymity, security, content filtering or network acceleration.{: .prompt-tip}

I used Burp as a proxy, and there started my troubles. In fact, I thought I would have to modify one of the HTTP request headers, especially the *User-Agent* which defines who is the client :

```http
GET /web-serveur/ch68/ HTTP/1.1
Host: challenge01.root-me.org
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/114.0.5735.199 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,/;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate
Accept-Language: fr-FR,fr;q=0.9,en-US;q=0.8,en;q=0.7
Cookie: _ga=GA1.1.1490808746.1689528313; _ga_SRYSKX09J7=GS1.1.1689528312.1.1.1689528382.0.0.0
Connection: close
```

Afters some research, I discovered the concept of **IP Spoofing**.

## IP Spoofing

IP address spoofing is a technique used to modify or mask the source IP address of a packet to make it appear as if it came from a source other than its true origin.

The **IP address** is a numerical identity assigned to each device connected to a network. IP address spoofing involves manipulating the IP header of a packet to change the source IP address, which can mislead recipients and security systems that rely on the IP address for identification and trust.

IP address spoofing can be used for malicious or illegal purposes, including :

1. **Distributed Denial of Service (DDoS) attacks** : Attackers can falsify their source IP addresses to mask their true identity and coordinate DDoS attacks by sending a large number of packets to a specific target, which can overload the network or targeted systems.
2. **Phishing attacks** : Attackers can use IP spoofing to mask their true identity and send e-mails or messages that appear to come from a trusted source in order to trick recipients into disclosing sensitive information or performing malicious actions.
3. **Filter or firewall spoofing** : By falsifying the source IP address, attackers can attempt to bypass filters or firewalls that rely on the IP address to authorize or block access to certain resources or services.

IP address spoofing is generally considered an illegal activity. Network security systems have implemented countermeasures to detect and prevent IP address spoofing, such as validating packet origin or configuring filtering rules. However, this technique can be used for legal and legitimate purposes in certain specific scenarios, such as security research or penetration testing.

## Resolution

IP Spoofing was exactly what I needed to do in order to find the password. I found an article on [Medium](https://medium.com/r3d-buck3t/bypass-ip-restrictions-with-burp-suite-fb4c72ec8e9c) which explains how to bypass IP restriction using Burp Suite. It involves this header :
![x-forwaded-for](https://miro.medium.com/v2/resize:fit:2000/format:webp/1*rxt3awwsC3t13_gMz-NCjA.png)
*The IP adress is randomly chosen in one of the ranges provided by the RFC 1918*

The HTTP **X-Forwarded-For** header is an optional header used in some proxy server or firewall configurations to indicate the real IP address of the originating user or client. It is used when the client connects via one or more proxies or intermediate servers. The header is added by the proxy or intermediate server when it forwards the HTTP request to the end server. It contains a list of IP addresses, where the leftmost IP address represents the real IP address of the original client. However, it's important to note that this header can be manipulated, and its use should be treated with caution in trusted environments.

Using **Match&Replace** settings, I added the X-Forwaded-For header in the HTTP Request :
![spoofing](/img/ip-bypass/spoofing.png)
*The header is on the last line*

After forwarding the request to the end server, I found the password !
