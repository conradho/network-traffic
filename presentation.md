%title: Playing in (Network) Traffic
%author: @ConradHo
%date: 2018/02/16



-> # The Journey of a HTTP Request through the Network Stack


-> *Django Girls Milton Keynes* <-

-> 2018/02/16 <-
-> Conrad Ho <-

-------------------------------------------------

@ConradHo
=========

* Full stack developer working at PythonAnywhere
    * Python, JS, and Go enthusiast
    * Novice data wrangler 
    * Vim fanatic

* New co-owner of a Labrador!

* Most recent fascination is cardistry (flourishing playing cards)
	* Some prior fascinations include photography, lock picking,
	  kiteboarding, chess, and poker

* In the next 10 minutes, we will talk about some network concepts,
  and intersperse some live demonstrations in between!
    * because live coding is fun and every day is an adventure :D
    * also to reinforce the concepts we just learnt
    * also you will be introduced to some commandline tools that may
      may eventually help you with debugging your django website!

* Slides for this presentation can be found [here](https://github.com/conradho/network-traffic)


-------------------------------------------------

IP and the Internet Layer
=========================

What happens when we make a http request?

> curl www.google.com

The network stack consists of multiple layers, the idea being that
each layer is modular and responsible for different things, with lower
layers doing more low level stuff before passing on processed packets
to higher layers.

The Internet Layer is near the bottom of the stack. Here, you have low
level protocols (eg: IPv4, IPv6) that deal with delivering a data
packet to a specific IP address (eg: 127.0.0.1).

-> *My Epic Post Office Analogy:*
-> *This is similar to a mail courier focused on delivering a*
-> *single package based on the Zip Code printed on it*

If you read one of the [first chapters](https://tutorial.djangogirls.org/en/how_the_internet_works/) of the Django Girls Tutorial,
you will remember that the internet is a network of connected machines.

Behind the scenes, the Internet Layer selects the gateway for the next
hop, and transmits the packet to this next-hop host.



-------------------------------------------------

Resolving Hostnames into IP Addresses
=====================================

You may have noticed that www.google.com is a domain and not a
numerical IP address (eg: 216.58.211.164).

A Domain Name System (DNS) lookup takes a domain name and finds out
the IP addresses so we can use the Internet Protocol to send packets
to that machine.

> nslookup www.google.com

In the early days of the internet, we used to store all hostnames
locally!

> cat /etc/hosts

-> *Fun tip:*
-> *Your local /etc/hosts file takes precedence over DNS lookups*


-------------------------------------------------

Yay Live Demo!
==============

Here's a couple tools that we can use to monitor network traffic, and
see the DNS lookup in action:

> sudo iftop -f "port 53"
> sudo tcpdump -nn port 53

Some [common ports](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers#Well-known_ports) include:
- SSH 22
- DNS 53
- HTTP 80
- HTTPS 443

-> *My Epic Post Office Analogy:*
-> *Ports are similar to going to a particular flat/unit number*
-> *to get to the particular office (eg: the dentist) after you*
-> *have arrived at the building*

-------------------------------------------------

TCP and the Transport Layer
===========================

The Transport Layer is one level above the Internet Layer. 

Some protocols in this layer are TCP and UDP.

It deals with issues such as network reliability (by using ACKs and
checksums to make sure packets are delivered and not corrupted),
maintaining order of packets, avoiding congestion by exponential
backoff etc.

-> *My Epic Post Office Analogy:*
-> *This is similar to a system where you deal with returned*
-> *mail, or choosing to resend a new package because it was*
-> *lost in transit, or send mail registered etc*

-------------------------------------------------

Yay Live Demo!
==============

You can do some pretty amazing stuff with just the Transport Layer.

The tool we will use here is ncat- you may also use nc, or netcat.

You can setup your own web server:
> echo hi, welcome to my website | ncat -l 8888
> curl localhost:8888

-> *Applying what we learnt:*
-> *Do you recognize* `localhost` *from /etc/hosts?*

You can roll your own chat messenger:

> ncat -l 8888
> ncat localhost 8888
> > hi

You can setup a backdoor shell:

> ncat -l -e /bin/bash 8888
> ncat localhost 8888
> > ls

-------------------------------------------------

HTTP and the Application Layer
==============================

The Application Layer includes protocols in this layer include
HTTP/HTTPS, FTP, IMAP, SMTP, NTP, DNS, and SSH.

These define interfaces with which we can interact with particular
applications running on the host machine. For example, HTTP defines
methods such as GET, POST, DELETE, PUT, and PATCH that you need to
specify in order to interact with a http web server.

This layer is the level at which we normally operate. ie. We do not
need to care about ensuring reliable Host-to-Host communication (dealt
with in the Transport Layer), nor do we need to care about lower level
stuff like how to get to a particular IP (the Internet Layer), nor
even lower level stuff dealing with physical hardware etc (the
Link/Network Interface Layer).

Example HTTP request (what your django webapp receives):

> GET /index.html HTTP/1.1
> Host: www.example.com

Example HTTP response:

> HTTP/1.1 200 OK
> Content-Type: text/html; charset=UTF-8
> Content-Encoding: UTF-8
>
> <html><body>Hello World</body></html>

-------------------------------------------------

Yay Live Demo!
==============

Let's take a look at http headers in real life:

> curl -v www.google.com

We can also do this manually

> ncat www.google.com 80
> > GET /index.html HTTP/1.1
> > Host: www.google.com
> >

You may have noticed that the ncat server we created previously
doesn't work when accessing localhost:8888 from your browser- this is
because we were not follow http specifications. Let's fix this!

> ncat -l 8888 < sample-http-response.txt

Now open up your browser developer tools and see if you recognize some
of the headers!
