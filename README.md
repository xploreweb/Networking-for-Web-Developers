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

**ONE LISTENING SERVER PER PORT**
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

An IPv4 address is actually a 32-bit or 4 octet value, which we usually write as a dotted quad. But writing an IP address as four decimal numbers is just a convention to make it easier to read. When it's used between computers it's just a 32 bit long string of bits.


# ADDRESSING AND NETWORK
Not all of the possible 32 bit IPv4 values are used for real addresses. Some of them are reserved for pecial protocols. Some of them are reserved for internal private networks. Some of them are for testing or for documentation. In fact, just over one-eighth of all possible IPv4 addresses, are set aside for something other
than addressing public hosts.

## Netblocks and Subnets
Computers that are on the same network, like an individual home or business or school, usually have IP addresses that are similar to each other. All of the addresses on a specific network block share a particular prefix. And computers that are on the same network block, can communicate with each other without going through a router. A network with a longer prefix has less of the 32-bit address left over to distinguish particular hosts, and a network with a shorter prefix has more addresses for hosts, so it's a larger network. For instance, if you have a network with a 24-bit prefix, that means there's eight bits left over for the host part of addresses, we would conventionally write this as /24.

## Subnet Masks
Subnet mask is another way to write a network size, which is a binary number made of ones and zeroes, to indicate the size of the network. Subnet masks is often seen in network configurations on hosts. Subnet masks are 32-bit values that are usually written as decimal dotted quads. The mask for a /24 network in binary, is 24 ones followed by eight zeroes and in decimal 255.255.255.0.

For /16 network a subnet mask wil be 255.255.0.0.<br>
FOr /14 network a subnet mask will be 255.252.0.0.

## Interfaces
It's more accurate to say that addresses don't belong to hosts so much as they belong to interfaces. A host is a laptop, and it can have multiple network interfaces and each interface can have zero or more addresses.

Some of the interfaces:
- a wired Ethernet interface 
- a WiFi interface
- a loop back interface - which can be used for talking to itself

And there are other kinds of interfaces as well, for example you might have a tunnel to another part of the network, virtual machine interface connecting the host operating system and the guest operating system etc.

## Routers and Default Gateways
A router is a device that connects two different IP networks. It acts as a gateway. Hosts on one network that want to send traffic to the other one, forward that traffic through the router. While most hosts might have only one interface with an interesting IPv4 address on it, a router will have two or more. The host on a local network knows about a default gateway, which is a router that's connected toward the rest of the Internet. Computers that are attached to the same switch or WiFi access point or other network hardware are normally local to each other. They can directly send packets to one another without going through a different network. And as we saw before they have IP addresses on the same net block.

## NAT
The router connects to ISP and gets a single real public IP address (ISPs to assign only one address to each household, office, or other customer), and then it assigns private addresses to all of devices in this private network. Private IP addresses come off of one of three reserved IP address netblocks. The most common private IP addresses found on home routers are in the network 192.168.0.0/24. With the default gateway of 192.168.0.1, this is only one of very many private IP address possibilities.

Private IP addresses are used with a system called NAT or network address translation. The way that it works is that whenever traffic goes between the private network behind the router and the public internet, the router has to rewrite or translate to the network addresses on it. The router maintains a map of
which inside addresses and ports are connected to what public internet addresses and ports.

This is specified in an internet standard called RFC 1918.

## Private and Public IP Address
Private addresses aren't private in the sense of being secret or personal. They're private, because they're only any good on the local network. They can't be used to directly on the public internet, because they're not actually unique. Thousands or maybe millions of people in the world are using IP addresses in the 192.168 range, but each one of these is behind a different NAT router.

There are three different blocks of IP addresses which are reserved for this private use. By design, these addresses should never be used on the public internet. They're used inside homes, offices, data centers, on mobile networks and so on. Web servers and other internet services don't see your private address. They see the public address.

## IPv6
All traffic on the Internet is split up into messages called packets. And each packet has the addresses of its source or sender and it's destination or intended recipient on it. Each server or other host that you want to talk to, it needs to have a distinct IP address. But there aren't enough IP addresses for all the computers today. There's about 4 billion IPv4 addresses available for hosts, and there's 7 billion people on the planet.

NAT is not a super great solution to the shortage of addresses in IPv4. It's a workaround. The solution is IPv6. IPv6 is the successor to IPv4. IPv6 fixes the address shortage problem by having much longer addresses, 128 bits or 16 octets long. That's 4 times as long as an IPv4 address. But that means that IPv6 addresses are kind of long.

So the world doesn't need nat with V6. There's enough addresses for every
device to get a unique public address. And IPv6 adoption started out very slow but it's been growing strong for several years now. IPv4 and IPv6 are different network spaces.

# PROTOCOL LAYERS
In Netcat is a TCP Session which lets two programs send strings of bytes back and forth over the network. TCP is kind of the middle layer of a stack of networking protocols or protocol stack which supports all sorts of different internet applications.<br>
HTTP and other applications -> TCP -> IP, the Internet Protocol

![image](https://user-images.githubusercontent.com/115207563/210768998-b7da7859-cead-4482-a52b-ad9ab3fb2b6a.png)

| PROTOCOL | concepts | where the code is | failures |
| --- | --- | --- | --- |
| HTTP | URLs, resources, verbs, cookies... | Flask, Apache, browsers... | error codes, slow response |
| TCP | ports, sessions, stream sockets | OS kernel, system libraries | broken connections, timeouts |
| IP | IP addresses, packets | OS kernel, routers | various |
| WiFi | access points, WPA passwords | device drivers | network unavailable |

## Ping and DNS in tcpdump
TCP dump is a tool what can use to display informatiom about network traffic between hosts and networks. 

To let tcpdump interpret correctly the information that going within a DNS query and response we will have to add the -s option for specifying the amount of data to capture for each packet. By setting it to 0 we will be able to capture the entire packet. This value actually is 262144 which it is far more data than a packet is going to usually handle (1500 would be closer but it also depends on the underlying network)

Also adding -n will prevent tcpdump to resolve IP addresses which for troubleshooting it is also a good thing to add. The final command would be: 'tcpdump -n -s 0 port 53' (DNS uses a port 53).

## HTTP in tcpdump
We can use tcpdump to look at the packets that the machine uses to talk to a web server. You can see a timestamp, IP address of the web server and a machine - the metadata.

## Sequence Diagrams
Sequence diagrams remind us to keep things in order and to make sure that our notions of what's going on in the network don't assume that servers can see the future.

![image](https://user-images.githubusercontent.com/115207563/210814345-6505677f-bd7d-4ddd-9c2d-8784a767491c.png)
Diagram of an HTTP connection using keep-alive.<br>
The client since an HTTP GET request with a connection keep alive header. The server sends a response and once a client has received that response it can send another request over the same connection and so on. A sequence diagram always represents some particular level of a protocol.

## TCP Flags
In low-level computer languages, a flag is a Boolean value — a true or false value — that is stored in memory as a single bit. If a flag bit is 1, we say the flag is set. If the flag bit is 0, the flag is cleared (or unset). Usually, flags come in groups, each of which can be set or cleared.

**The six basic TCP flags**<br>
The original TCP packet format has six flags. Two more optional flags have since been standardized, but they are much less important to the basic functioning of TCP. For each packet, tcpdump will show you which flags are set on that packet.
- SYN (synchronize) [S] — This packet is opening a new TCP session and contains a new initial sequence number.
- FIN (finish) [F] — This packet is used to close a TCP session normally. The sender is saying that they are finished sending, but they can still receive data from the other endpoint.
- PSH (push) [P] — This packet is the end of a chunk of application data, such as an HTTP request.
- RST (reset) [R] — This packet is a TCP error message; the sender has a problem and wants to reset (abandon) the session.
- ACK (acknowledge) [.] — This packet acknowledges that its sender has received data from the other endpoint. Almost every packet except the first SYN will have the ACK flag set.
- URG (urgent) [U] — This packet contains data that needs to be delivered to the application out-of-order. Not used in HTTP or most other current applications.

If the server doesn't want to accept the connection, it may not send anything at all. Or it may send a packet with the RST flag

## Timeouts and Errors
TCP has a number of built in timers. If it takes too long to hear back from the server we're trying to connect to, TCP will abandon the attempt to make a connection and give an error to the application.

The following problems might cause a TCP session to time out for instance between a browser and a web server
- the host at the other end is powered off
- the cable loss between you and your ISP
- could a timeout because you're connecting to a server that doesn't exist

# BIG NETWORKS
Here are some of the ways that network speed and other properties affect your users experience of your web apps.

You can see all of the hops involved in getting your traffic from you to a distant server by using a traceroute tool. The most common traceroute tool is called just that, traceroute. You can give it a host name or an IP address and it will display the route all the IP addresses of all the routers that it took for traffic to get there. There are more advanced traceroute tools such as MTR which will repeatedly trace and can sometimes show you different routes the traffic may take.

**Ping Mechanism**<br>
When you've used Ping, it's given your numbers for the round trip time between you and the machine you're pinging.<br>
A round trip time is how long it takes for a packet to get from you to the other end. But ping can tell how much time has elapsed between when it that packet and when it received a response.

**Traceroute/TTL Mechanism**<br>
The ability to trace out packet paths wasn't intentionally built into the Internet protocols. The safety feature traceroute makes use of. Every packet has a time to live or TTL field, which starts at some large number and is reduced by one each time that packet hits a router. As it moves through the network, each router reduces the TTL on the packet by one as it passes it on, all the way until it finally gets to its destination. This means that if routers are misconfigured so that packets flow around in an infinite loop, eventually the time to live (TTL) on each packet will drop to zero, and it will expire. This helps keep momentary loops from crashing large parts of the network with an overload of traffic. When a packet's TTL expires, the router that last received it, sends a tiny error message back to the packet's original sender. And that message says that the packet's TTL expired, and it gives the address of the router that saw that packet die.

Traceroute and tracert are computer network diagnostic commands for displaying possible routes (paths) and measuring transit delays of packets across an Internet Protocol (IP) network. The history of the route is recorded as the round-trip times of the packets received from each successive host (remote node) in the route (path); the sum of the mean times in each hop is a measure of the total time spent to establish the connection. Traceroute proceeds unless all (usually three) sent packets are lost more than twice; then the connection is lost and the route cannot be evaluated.

## Bandwidth
The maximum amount of data transmitted over an internet connection in a given amount of time.

## Firewalls & Filtering
Firewalls are devices that network operators can use to filter traffic that's coming into or leaving their network. A firewall is one example of a class of network devices called middleboxes — devices that inspect, modify, or filter network traffic. 

A firewall can be a real boon to an organization's network security. The most common configuration for a firewall is to drop any incoming traffic except traffic to (host, port) pairs that are supposed to be receiving connections from the Internet. This lets the network administrator be sure that other machines on the network — like backend databases or administrative systems — aren’t going to get direct attacks from outside.

## Proxies & NAT
With NAT, several devices can access Internet resources through a single public IP address, with the NAT device using port numbers to match up connections on the inside and outside.

For end-users, NAT devices overlap with firewalls. Typical home routers can act as both a NAT and a simple firewall, often having the ability to block or filter at a very basic level. At a larger scale, ISPs and other organizations have deployed NAT devices for their whole customer networks, called carrier-grade NAT. This is very common for mobile networks, and also for ISPs in the developing world, where there never were anywhere near enough addresses allocated for the number of users.

Another way that can happen is through the use of web proxies. Whereas a NAT works at the IP level, rewriting packets, a web proxy works at the HTTP level, taking queries from browsers and sending them out to web servers. Many organizations use web proxies for caching, including some ISPs. From the standpoint of a web developer or site operator, traffic from a busy proxy looks much the same as traffic from a busy NAT: queries for many users, on many actual computers, are funneled through a single public IP address.
