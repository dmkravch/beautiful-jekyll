---
layout: post
title: SIPp + CUCM 11.5
subtitle: Simple yet powerful way of load testing Cisco Call Manager with basic/enhansed call scenarios
tags: [testing, cucm, load]
---
Recently, I've decided to load a CUCM with simple calls and see how it would behave.  
My setup was: 
CUCM 11.5.1.12900-21 (2,500 users OVA)  
[SIPP 3.3.990](http://sipp.sourceforge.net/)

SIPp is a free Open Source test tool / traffic generator for the SIP protocol. It includes a few basic SipStone user agent scenarios (UAC and UAS) and establishes and releases multiple calls with the INVITE and BYE methods. It can also reads custom XML scenario files describing from very simple to complex call flows. It features the dynamic display of statistics about running tests (call rate, round trip delay, and message statistics), periodic CSV statistics dumps, TCP and UDP over multiple sockets or multiplexed with retransmission management and dynamically adjustable call rates.

## Installing SIPp
* On Linux, SIPp is provided in the form of source code. You will need to compile SIPp to actually use it.
* Pre-requisites to compile SIPp are :
 * C++ Compiler
 * curses or ncurses library
 * For TLS support: OpenSSL >= 0.9.8
 * For pcap play support: libpcap and libnet
 * For SCTP support: lksctp-tools
 * For distributed pauses: Gnu Scientific Libraries

 You have four options to compile SIPp:
Without TLS (Transport Layer Security), SCTP or PCAP support: - This is the recommended setup if you don't need to handle SCTP, TLS or PCAP.
~~~
tar -xvzf sipp-xxx.tar               
cd sipp               
./configure               
make
~~~             
With TLS support, you must have installed OpenSSL library (>=0.9.8) (which may come with your system). Building SIPp consists only in adding the "--with-openssl" option to the configure command:
~~~
 tar -xvzf sipp-xxx.tar.gz              
 cd sipp              
 ./configure --with-openssl             
 make
~~~            
With PCAP play support:
~~~
tar -xvzf sipp-xxx.tar.gz             
cd sipp             
./configure --with-pcap             
make
~~~             
With SCTP support:
~~~
tar -xvzf sipp-xxx.tar.gz             
cd sipp             
./configure --with-sctp             
make
~~~             
You can also combine these various options, e.g.::
~~~
tar -xvzf sipp-xxx.tar.gz             
cd sipp             
./configure --with-sctp --with-pcap --with-openssl
~~~    
Source: [uac.xml](uac.xml) 

After that the SIPp tool should be ready for use. SIPp allows to generate one or many SIP calls to one remote system. The tool is started from the command line. In this example, two SIPp are started in front of each other to demonstrate SIPp capabilities.

Run sipp with embedded server (uas) scenario:
~~~ 
./sipp -sn uas
~~~ 
On the same host, run sipp with embedded client (uac) scenario

~~~ 
./sipp -sn uac 127.0.0.1
~~~ 
And we should be able to see the statistics of the calls. By default, it generates 10 calls per second. In order to change it, we can use the "-r" parameter, for example ./sipp -sn uac 127.0.0.1 -r 50 to generate 50 calls per second. And then, we also can verify that the CPU is being consumed more extensively by running to or htop commands.

## Configuring CUCM 

1. Registering a phone.
   1. Enable auto answer
   2. Configure  "Multiple Call/Call Waiting Settings" for 200:  

[![alt text](/img/pastedImage_1.png "Multiple Call/Call Waiting Settings")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/pastedImage_1.png)

2. Creating two SIP trunks (one incoming and one outgoing, in my example I used two different CUCM clusters in order to overcome an issue that we cannot use the same IP of an incoming/outgoing trunk IP, of course we could change some incoming/destination ports, but as I had two clusters I've decided to use them both). 
   1. One incoming from the SIPp server, which will have a CSS with the access to a registered phone and to the outgoing trunk.
   2. Configure an outgoing trunk with the destination of the other CUCM server. 
   3. On the CUCM2 configure an incoming trunk from the CUCM1.
   4. On the CUCM2 configure a SIP trunk to the SIPp server.
   5. Create a route pattern, for example 30XX to route all the calls to those number to the SIPp server.

[![alt text](/img/pastedImage_4.png "Multiple Call/Call Waiting Settings")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/pastedImage_4.png)

## Running SIPp 

And finally, we can run the SIPp server and client to create the SIP traffic.
On the server side: 
~~~
sudo ./sipp -sn uas -i 10.51.115.155 -p 5060 -trace_err
~~~
On the client side: 
~~~
./sipp -sf uac.xml 10.52.118.116:5060 -s 3000 -i 10.51.115.155 -r 20
~~~

Where uac.xml is a simple xml with the scenario for a simple call. Can be found at http://sipp.sourceforge.net/doc/uac.xml.html 

{: .box-note}
3000 - destination  
10.52.118.116 - IP of the CUCM  
-r 20 - 20 calls per second  

And here are the results. Server side:

[![alt text](/img/pastedImage_2.jpg "The results. Server side")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/pastedImage_2.jpg)

Client side:
[![alt text](/img/pastedImage_3.jpg "Multiple Call/Call Waiting Settings")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/pastedImage_3.jpg)

As we can see and can judge, in the current setup the CUCM cannot process 20 calls per second, which leads to some retransmits and failed calls.  

And it's also proved from the vCenter performance tab of the respective server:
[![alt text](/img/pastedImage_5.png "Multiple Call/Call Waiting Settings")](https://raw.githubusercontent.com/dmkravch/dmkravch.github.io/master/img/pastedImage_5.png)

Thank you. 
