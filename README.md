# NETWORKING FOR WEB DEVELOPERS
Summary of the Udacity free course Networking for Web Developers<br/>
https://www.udacity.com/course/networking-for-web-developers--ud256

## Introduction
Before taking this course, it's also useful to follow the Audacity courses
- Linux Command Line Basics
- Configuring Linux Web Servers
- Designing RESTful APIs

To do the exercises in this course, I'm using Linux-based hosting service https://cloud.google.com/shell

# FROM PING TO HTTP
Ping is an individual 'package' what will be sent to an exact computer and back to sender. Ping can give us information:
- Our computer has Internet access
- The destination computer - is up and running
- There is a website, with the destination ip we entered
- Our computer is directly on the destination computer's network
- The ISP knows how to send traffic towards to the destination computer

Ping is simplier than HTTP, but HTTP is not based on ping. 
Ping is usually implemented as an ICMP echo request. A simpler datagram protocol: You send a packet, the server replies with the corresponding packet and that's about it. HTTP is requesting a webapage, what needs a web server. Ping doesn't need a server, just any operating system what supports internet access.

Sending the request: "printf / HTTP/1.0\r\n\r\n" prints a header as well as body, and the connection closes after the response.

The Netcat ( nc ) command is a command-line utility for reading and writing data between two computer networks. The communication happens using either TCP or UDP.

| (vertical bar) is a pipe between input and output program, take it from input and fit to the output.

An Example:<br>
printf 'HEAD / HTTP/1.1\r\nHost: en.wikipedia.org\r\n\r\n' | nc en.wikipedia.org 80
(output of printf string as a input for the nc program, host: what web site do we want from that host; nc: what host should nc connect to?)

**PRINTF AND NETCAT LAYERS**<br>
printf and nc are two different layers of actvity, where printf gives a HTTP request, and nc sedning string over network and get the respond back.

| Layers | Protocols | Concepts |
| --- | --- | --- |
| Application | HTTP, SSH | URLs, passwords |
| Transport | TCP, UDP | port numbers, sessions |
| Internet | IP | IP addresses, routes |
| Hardware | WiFi, Ethernet, DSL | signal strength, access points |

The nc (or netcat) utility is used for just about anything under the sun involving TCP, UDP, or UNIX-domain sockets.  It can open TCP connections, send UDP packets, listen on arbitrary TCP and UDP ports, do port scanning, and deal with both IPv4 and IPv6.  Unlike telnet(1), nc scripts nicely, and separates error messages onto standard error instead of sending them to standard output, as telnet(1) does with some.

Common NC uses include:
- simple TCP proxies
- shell-script based HTTP clients and servers
- network daemon testing
- a SOCKS or HTTP ProxyCommand for ssh(1)
- and much, much more...

**LISTENING THE PORT**<br>
Listening on a port is a very simple sort of being a server. A program that listens on a TCP port is waiting for another program to connect to that port. Once that happens, the two programs can send data back and forth.
 
**TALKING TO SERVERS**<br>
It is sometimes useful to talk to servers “by hand” rather than through a user interface.  It can aid in troubleshooting, when it might be necessary to verify what data a server is sending in response to commands issued by the client.  For example, to retrieve the home page of a web site:<br>
$ printf "GET / HTTP/1.0\r\n\r\n" | nc host.example.com 80<br>
Note that this also displays the headers sent by the web server.  They can be filtered, using a tool such as sed(1), if necessary. 

**PORT NUMBERS**<br>
The highest port number is 65535.
On most systems the lowest 1,024 ports from 0 up to 1,023, are reserved for programs that are started by the system superuser account or root on Unix. It's like those ports are reserved parking spots. That's why when you start up a web server which runs on port 80 you have to do it as root or with the sudo command. For security a web server gets rid of its root privileges once it starts up. But it needs to start with them so it can listen on port 80. So if you use sudo to run nc as root, it can listen on port 1,000 just fine.

**ONE LISTENING SERVER PER PORT***
Normally, only one program can listen on a given port on a machine at a time. But once a program starts, it can run threads or child processes that handle incoming connections on a port, or a loop between several connections and handle all of them. That's what a web server does to be able to handle more than one connection at a time. NC-L doesn't do that. It's not really a full server as such just sort of a listener that only accepts a single connection.

There's a standard Linux program that you can use to find out which programs are listening or otherwise, using network connections. The lsof program, name stands for list open files, and it has an option -i, to make it list internet sockets specifically. You'll have to run it using sudo to give it root privileges.

***

Net Cat gives us a simple model of a program using the network. But it's a lot simpler than the that a standard web browser and server use the network. Browsers and servers don't just handle a single request or a few requests one after the other. They also work on several requests at the same time. When your browser loads a web page from a server. It usually has to load a lot of different files. The HTML itself, plus images, scripts, CSS, and other data. The more that it can do this in parallel the faster it can render that page onto your screen.
Data that the server is sending to you isn't in sync with what it's sending to that other person's browser. And there are a lot of different ways that programs can handle multiple connections at once.

One of the most basic ones is what early web servers did:whenever a new connection comes in, the server process forks, it asks the operating system to split it in two, like a cell dividing. A different model that many more recent servers use is to create a pool of processes or threads, each of which can handle one connection at a time. And this is faster than starting new processes. But it does have the downside that there's a limit to how many requests may be handled at once before
things start to slow down.

And finally a third model is to have a single process that quickly switches between handling requests as they become available.

Before your server can handle a request, the client has to know how to get in touch with your server - DNS.


# NAMES AND ADDRESSES

An IP address is used to designate a particular destination on the Internet, typically a particular machine. Every message that travels over the Internet, a web request, an email, a ping etc is split into packets, and every one
of these packets has the IP address of the machine it came from and
where it's going to.

**HOST**<br>
is just a machine on the Internet, particularly one that might host services.

**ENDPPOINTS of a connection**<br>
are two machines or programs that are communicating over connection.

## Ping a hostname
If you take the name of a host, and ping it, you'll see an IP address along with the ping results. The ping command has to look up the IP address of the host first 
before it can actually send it a ping message. Ping keeps running forever until you stop it with Ctrl+C. But you can tell it how many pings to do with a -c option. For instance, -c 7 will have it send just 7 pings and then exit.

## DNS Domains
Hostname (www.example.com) gets translated into an IP address through the Domain Name System,or DNS. DNS is a worldwide distributed directory of network information. It stores a wide variety of records but the best known kind of DNS
record is the A record. A for address, which maps a name like www.example.net to an IPv4 address. Client programs such as web browsers look up these records in order to find the address of a web site or another service. The creator of a website needs to set up DNS records for it so that users can access it by name.

##The Host Command
The host command is a basic utility for looking up records in DNS. It will query your OS name service. Which usually ends up sending a request to whatever DNS server your computer's configured to use. There are lots of kinds of DNS records but the default one for looking up the addresses of IPv4 hosts is an a type of record for address.

## Dig Command for the DNS Records
The 'dig' command is a same kind of information as host, but in a more technical and complete form. The dig tool shows the same information more or less, but in a form that's more readable for scripts and is closer to the way that it's stored in the DNS server's configuration file. 

Dig has sections:
- Query
- Question
- Answer
- Additional information

## DNS Record Types
There are many different types of DNS records. Literally, there are dozens of them.
- A records - to look up the IPv4 address of another host.
- CNAME - stand for canonical name. Used to make an alias from one name to another.
- AAAA (the quad-A record type)- stand for an IPv6 address
- NS record type - stand for a DNA name server. An NS record for a particular domain says what DNS servers have the records for that domain.

## DNS Cashing Server

DNS is a distributed directory. Around the world there are several root servers which direct requests to top level domain servers (.com, .net, .org and some others). This means that no one DNS server needs to know all the records for all the names and domains in the world. The records for a particular domain, will be found in the authoritative name servers for that domain.

To make it faster, and more complicated, the DNS server that the client to resolve talks normally to a nearby caching DNS server. For a home user, that might be the local home router, or it might be run by their ISP, or it could be a well known
DNS service like Google Public DNS. When caching server receives a query from a client, first it consults its local cache. If the cache doesn't know what the record is, the caching server recursively resolves the query, forwarding to
the appropriate nameservers. Possibly first at the root and then at the top level domain, and then at the lower level domain until eventually it gets back an answer. Then it stores that result in its cache so it doesn't have to run the same query over and over again every time you look it up.

Caching can cause problems, when the IP address of the web site has been changed, then clients who use that cache will see the old address instead of the new one. To cut down on this kind of thing DNS records have a time to live, or TTL, which tells caches how long to cache them for. After the TTL expires, caching servers have to go back to the authoritative server again, look the record up, to make sure that their information is fresh.

## DNS and HTTP
Domain names are also essential to several HTTP features, including cookie security and SSL.. A single web server can handle requests from multiple sites which it tells apart by their domain names.

- Apache calls this a virtual host configuration.
- NGiNX calls it having multiple server blocks.

You have to tell that web server what host name, really what domain name, you're expecting to talk to. When a web app sets a cookie, it sets that cookie for a particular domain name, and further requests to that demand will get that cookie sent back. SSL encryption certificates are issued for particular domains. SSL serves several different security purposes. By encrypting the traffic between browser and server, it prevents networks in the middle from reading private data. But it also lets the user's browser verify that the site they're getting data from is actually the site that they expect.

##IPv4 and IPv6
There are two major versions of the Internet protocol in use today: IPv4 and IPv6. IPv4 is the older one that the majority of Internet traffic is using today.

The IPv4 addresses are usually written as dotted quads. That's four numbers separated by dots (8.8.8.8). Each of these four numbers is one byte or octet, or 8-bits, which means that mathematically it could be a value from 0 to 255.

Each address has to be different from every other address on the same network. With a given number of bits you can only make a certain number of distinct values.



# ADDRESSING AND NETWORK
...

# PROTOCOL LAYERS
...

# BIG NETWORKS

...

The course is in progress...
