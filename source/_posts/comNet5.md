---
title: DNS
tags: computer network
categories: notes
---
> Domain Name System, does translation work, is an application-layer protocol to map host name to IP address.

## The DNS is :
1. a distributed database implemented in a hierarchy of DNS servers.
2. an application-layer protocol that allows hosts to query the distributed database.

## How DNS work in network
DNS is commonly employeed by other application protocol such as HTTP and SMTP. For example, user surfs online.
![DNS work process]( /img/my43.jpg)

## How DNS scale
![DNS is a distirbuted, hierarchical Database instead of centralization.]( /img/my44.jpg)

For example: we are looking for `www.amazon.com`, go to root DNS, which returns IP of the responsible TLD server for top-level domain( TLD ) `com`. The TLD server finds the IP of the responsible authoritative server for `amazon.com`. Eventually, the authoritative server returns the IP for the host name `www.amazon.com`.

root server -> TLD( top-level domain server) -> authoritative server -> ISP.

## Local DNS
> It acts as a proxy, is very closed to and the central of the residential or institutional ISP.

ISP sends host name to local DNS, local DNS translates the alia to an official host name then sends to root server, root server sends next responsible TLD back to local DNS. Local DNS sends reuqest to TLD, TLD sends the responsible IP of authoritative server to local server. Finally, local server sends requets to authoritative server. Completed IP address is found.

![Recursive queries: 1 - 2 - ... - 8; Iterative queries: 2 - 3, 4 - 5, 6 - 7. ]( /img/my45.jpg)

In theory, any DNS query can be iterative or recursive.
+ Recursive: from request host to bottom DNS server until figuring out the IP address, then response back by layer.
+ Iterative: from request host to local DNS, then each DNS server responses to local DNS, and local DNS contact all DNS servers until figuring out the IP address.

**DNS caching**: each layer of DNS caches what it mapped. This cache is generally kept two days, depends on its level. Top level freshes less, bottom level freshes more.

## DNS Records
A resource record is a four-tuple that contains the following fields: `(Name, Value, Type, TTL)`. The meaning of `Name` and `Value` depend on `Type`. `TTL` indicates the cache time. Think records as the record in database.


## DNS Messages
![The format of messages between DNS servers]( /img/my46.jpg)

The Microsoft SQL Server virus, Slammer, had a side effect of effectively disabling 5 of the root name servers in the Internet.  How could this have affected the functioning and performance of the Internet?

Answer: 
+ In theory, there should have been little impact to performance because:

    + the LDNS caches entries to root and tld name servers, there are rare cases, that LDNS will have to query root, typically tld name servers would be sufficient.
    + there is a redundancy in root servers. we have 13 root servers that are geographically dispersed, if a root server was compromised, the requests will be redirected to another root server.
    + the LDNS also caches web server IP addresses.
+ In practice, it dramatically slowed the Internet due to a compound effect of routers (not just DNS servers) becoming overwhelmed. You can read more about it on wikipedia  




> Reference material: 
> Book: Computer Networking A Top-Down Approach 7th edition, Jim Kurose & Keith Ross, Addison-Wesley. 
> Slides: University of Waterloo, ECE 456/656 (Computer Networks), 2020 spring term, Professor Zille Huma Kamal.









