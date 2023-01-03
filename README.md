# Networking-for-Web-Developers
Summary of the Udacity free course Networking for Web Developers<br/>
https://www.udacity.com/course/networking-for-web-developers--ud256

## Introduction
Before taking this course, it's also useful to follow the Audacity courses
- Linux Command Line Basics
- Configuring Linux Web Servers
- Designing RESTful APIs

To do the exercises in this course, I'm using Linux-based hosting service https://cloud.google.com/shell

# From Ping to HTTP
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

Näide: 
printf 'HEAD / HTTP/1.1\r\nHost: en.wikipedia.org\r\n\r\n' | nc en.wikipedia.org 80
(output of printf string as a input for the nc program, host: what web site do we want from that host; nc: what host should nc connect to?)

**PRINTF AND NETCAT LAYERS**
printf and nc are two different layers of actvity, where printf gives a HTTP request, and nc sedning string over network and get the respond back.

| Layers | Protocols | Concepts |
| --- | --- | --- |
| Application | HTTP, SSH | URLs, passwords |
| Transport | TCP, UDP | port numbers, sessions |
| Internet | IP | IP addresses, routes |
| Hardware | WiFi, Ethernet, DSL | signal strength, access points |



# Names and Addresses
...

# Addressing and Network
...

# Protocol Layers
...

# Big Networks

...

The course is in progress...
