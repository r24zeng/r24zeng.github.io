---
title: Web and E-mail principle
tags: computer network
categories: notes
---
> Web uses defualt persistent HTTP (application protocol) on TCP (transport protocol); E-mail uses defualt persistent SMTP (application protocol) on TCP (transport protocol).

## Web
Web page consists of objects. They are referenced by URL, eg, *www.someschool.edu (host) / someDept/pic.gif (path name)

### HTTP
> Full name is Hypertext transfer protocol. It's an application layer protocol.

HTTP includes HTTP request and HTTP response, which consists of head lines and body lines.

![HTTP request format, `\r\n` means one line ends, empty line with `\r\n` means message ends]( /img/my27.jpg)

**HTTP request method types**: Common used (HTTP/1.1 version): GET (empty body, appends URL to get page from server), POST (fills body field, sends data to server), HEAD (request the head fields from server), the three are also in HTTP/1.0. PUT and DELETE.

**HTTP/1.0**: first version of HTTP, non-presistent HTTP. <br>
**HTTP/1.1**: second version of HTTP, presistent HTTP. Default exchanging message by pipline (after handshaking and one base page round, can send all objects back-to-bcak, also recieve them back-to-back, save time). It also can set non-pipline, which means send one object in one round continuously.

![HTTP response format]( /img/my29.jpg)

HTTP process using TCP between client and server overview:
+ client initiates TCP connection (creates socket) to server, port 80
+ server accepts TCP connection from client
+ HTTP messages (application-layer protocol
messages) exchanged between browser (HTTP
+ TCP connection closed

**Sample code**:
+ 200 OK
    + request succeeded, requested object later in this msg
+ 301 Moved Permanently
    + requested object moved, new location specified later in this msg
+ 400 Bad Request
    + request msg not understood by server
+ 404 Not Found
    + requested document not found on this server
+ 505 HTTP Version Not Supported

**Non-presistent HTTP/1.0**: <br>
TCP connection closes after 2RTT.
![**RTT** is the time for a small packet to travel from client to server and back. In this case, total response time is (2RTT + 1 file transmission time), after this object, the TCP connection closes. Adding one more object will take (2RTT + transmission time), web page doesn't need too much tansmission time]( /img/my30.jpg)

**Non-presistent HTTP/1.0 with `n` parallel TCP connections**: <br>
TCP connection closes after 2RTT.
![If request 6 objects, 3 parallel connections, then (2RTT) for web page, (2RTT) for 3 object, so (6RTT + 2 file transmission time) in total]( /img/my33.jpg)

**Presistent HTTP/1.1 without pipeline**: <br>
TCP connection closes after all objects sent.
subsequent HTTP messages between same client/server sent over open connection. Response time is (RTT+transmission time) per object except the first one - web page (takes 2RTT).

![If request 8 objects, then (2RTT) for web page, (1RTT + transmission time) for each object, so (10RTT + 8 file transmission time) in total]( /img/my31.jpg)

**Presistent HTTP/1.1 with pipeline**: Connection keeps open until all objects sent.  Also after web page (first tell the server how many and what objects I need to send to a specific URL), objects can be sent back-to-back (no need to wait for response).
![If request 8 objects, then (2RTT) for web page, (1RTT) for 8 object, so (3RTT + 1 file transmission time) in total]( /img/my32.jpg)

This method causes HDL blocking problem. If one object has error or crash, other objects are stuck. Then another method called *HTTP/2.0 (multiplexing request/response)* is proposed. The objects will be sent by server out of order and don't wait when error happens.

**HTTP is stateless: server maintains no information about the past client requests. This is why some webs use cookies.**

### Cookies:
Four components:
1. cookie header line of HTTP response message
2. cookie header line in next HTTP request message
3. cookie file kept on users host, managed by users browser
4. back-end database at Web site

![regular cookie example. 第一次访问时，server给这个用户建一个ID缓存，下次要用时发送请求直接附加上用户缓存ID]( /img/my35.jpg)

### Web caches (proxy server)
一旦用户已经有缓存ID了，那么中间就相当于有一个代理服务器，用户发送请求先到达代理服务器，若数据存在则直接返回给用户，若不存在再由代理服务器发送请求至原服务器，原服务器返回给代理服务器，代理服务器缓存数据，并最终返回给用户。主要优点：节约时间，减少拥堵。

Linux/Unix HTTP client-server interface code demo:
![Run on my Mac Unix. The web port is generally "80".]( /img/my34.jpg)

## E-mail
> User agent, mail server, simple mail transfer protocol **SMTP** (application layer protocol) with **TCP** (transport protocol).

Three phases of transfer: handshaking, transfer of messages, closure. Message must be in 7-bit ASCII (*Very different from HTTP, HTTP can transfer any type of data*). **SMTP** uses presistent connections.

![Whole process of A sending email to B]( /img/my37.jpg)

SMTP commands include *HELO, MAIL, FROM, RCPT TO, DATA, CRLF.CRLF, QUIT*.

If reciever's server is down, the mail remains in sender's mail server and waits for a new attempt.

Note that Bob's user agent can't use SMTP to obtain the messages because obtaining the message is a pull operation, whereas SMTP is a push protocol. So Bob needs a mail access protocol that transfers messages from Bob's mail server to his local PC (protocols such as POP3, IMAP and HTTP). <br>

**POP3**: It has three phases: authorization, transaction and update. It uses four commands: *list, retr, dele and quit*. in order to avoid the download-and-delete mode so as to Bob can read the email from different PC, in download-and-keep mode, the user agent leaves the messages on the mail server after downloading them.

**IMAP**: Unlike POP3, an IMAP server maintains user state information across IMAP sessions - for example, the names of the folders and which messages are associated with which folders. It allows user to maintain a folder hierarchy on a remote server that can be accessed from any computer.

**HTTP**: If A sends email from its browser to her mail server, HTTP protocol will be preferred.

Linux/Unix E-mail code demo:
![The host name of the client is "crepes.fr", the hostname of the server is "mxer.uwaterloo.ca", mail port is generally "25".]( /img/my38.png)

## Comparison of HTTP and SMTP:
HTTP: pull (mostly). 

SMTP: push.  <br>

HTTP: any data type. 

SMTP: each message including the body of each message, to be in 7-bit ASCII fomat. 

HTTP: each object encapsulated in its own
response message. 

SMTP: multiple objects sent in multipart message. <br>

> Reference material: 
> Book: Computer Networking A Top-Down Approach 7th edition, Jim Kurose & Keith Ross, Addison-Wesley. 
> Slides: University of Waterloo, ECE 456/656 (Computer Networks), 2020 spring term, Professor Zille Huma Kamal.


