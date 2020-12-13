---
title: Web knowledge conclusion
tags: web
categories: self study
---
# 1. Internet Primer/DHCP/DNS/Access Points
![my1]( /img/my1.jpg)
How do human and computer interact to Internet? **Internet Primer**

**IP address:** 
- Identity Protocal
- Unique identity of your computer on the Internet.
- Basic IP address look like: W.X.Y.Z  , each charater in range of `[0,255]`
- Unique ID to recieve or send infomation to others

---
How does my computer get my IP address? **DHCP**

**DHCP:**
- Dynamic Host Configuration Protocol
- Assign IP address to you
- IP can refer to any website (74.125.202.138 is one of Google's IP)

---
Memorize each IP of webs is chanllenge, just like to remember all phone numbers of your friends, how to solve this? **DNS**

**DNS:**
- Domain Name System 
- Image it as local yellow page, map URL to IP of visit any web
- Image large DNS as a aggregator of local yellow pages of the wold, such as Google, which manages many IP to point to Google

---
**Access Points**
- Roll somethings together, such like router, switches
- I understand it like local internet 
- Local community share a public IP to connect to others

---
![my2]( /img/my2.jpg)
> Actually internet doesn't a kind of endpoint, it means users' connectection with each other.

# 2. IP/TCP
**IP:**
- Identity Protocal
- It is the rule how point A gets information from point B
- Intermdian **router**'s job is to move traffic from cental to server, point a to point b, one step by one step
- Split information into packets so that if one part of information drops will be not costly
- If more path from A to b is better, because once encounter traffic jam there is still another path to go
![my3]( /img/my3.jpg)

---
**TCP:**
+ Transmission Control Protocol
+ Guarantee delivery
+ It has **port** number and number of packages
    + FTP(file transfer port) uses port 21
	+ SMTP(e-mail) uses port 25
	+ DNS uses port 53
	+ HTTP(web browsing) uses port 80
	+ HTTPS(secure web browsing) uses port 443
+ TCP breaks infomation to chuncks and remember the order and port of the package. Inside of TCP layer, IP remember the end address and control the route. Once IP confirms reach the reciever, it stops and show TCP layer. TCP confirm all packages reach the reciever.

---
# 3. HTTP
**HTTP:**
- Hypertext Transport Protocal
- A kind of rule or format how to send request and how to response request
- Can see in Chorm browser: `view -> developer -> develper tool -> request -> view source` or `view -> developer -> develper tool -> response -> view source`

# 4. HTML
**HTML:**
- Hypertext Markup Language
- Edit the structure of web page

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <title>
			Hello world!
		</title>
    </head>
    <body>
        <h1>Hello world!</h1>
    </body>
</html>
```

# 5. CSS
- Cascading style sheets
- A kind of style sheet language
- Edit the style of web page, such as font-size, position, color and centering

# 6. JavaScript
- Make website dynamic 
- Very similar to C language
- No variable type and function return or not

