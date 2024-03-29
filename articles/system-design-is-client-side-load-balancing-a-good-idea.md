---
title: System Design - Is client side load balancing a good idea?
date: '2021-03-15'
tags: ['system design']
draft: false
summary: Client side load balancing is a barely touched area while designing systems. Is client side load balancing, a good idea?
author: the2ndfloorguy
---


Load Balancer (LB) is another critical component of any distributed system. Before, directly diving into client side load balancing we need to first understand about web application servers and load balancing.

## How a typical web application behaves?

When we type a URL in the browser, the browser looks up the IP address for the domain name via DNS and a HTTP (Hyper Text transfer protocol) request is sent to the web server. The server sends back a HTTP response and browser begins rendering the HTML.

A lot of other things also happen in between like browser checking 4 caches (browser cache, OS cache, router cache & ISP cache) for server IP address before initiating a recursive DNS query to find IP address, if not found in cache. After that, the browser initiates a TCP connection with the server for data transmission. 

## What is Load Balancing ?

Let's say you have a website which shows latest movie trends. One day, your website gets quite popular and lots of people want to use your website. Now, your server is not capable of handling these many requests so what you do, you increase resources (RAM, CPU cores etc) to handle more requests in your machine (`That's Vertical Scaling`) BUT there is a limitation on increasing resources in a machine.  

So, you decided to add few more new machines, running the same application and distributed the traffic among all nodes. (`This is called Horizontal Scaling with load balancing`) 


![horizontal-vs-vertical-scaling-diagram.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1615743749090/ddDUdm_Zr.png)

As the term stats, load balancing refers to efficiently distributing incoming traffic across a bunch of backend servers. Load Balancer sits in the middle of your browser and web servers, receiving and then distributing incoming requests (using round robin or some other fancy algorithm) to any available server capable of fulfilling them.

![1539540626018.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1615696959723/QWKZWTqv8.png)
 
## Common approaches for load balancing?

There are many techniques of achieving load balancing but generally they should meet the following requirements -

- Distribute load among a cluster of application servers
- Handle fail-over of an application servergracefully
- Ensure the cluster of servers appears as a single server to the end user

## Below are some commonly used approaches -


### Round robin DNS :

This is a popular yet simple approach to balancing web requestes. In this approach, we create multiple DNS entries in the DNS record for the same domain. 

**For example** - 

If I want to balance load on 'https://pankajtanwar.in' and I have two web servers with IP address of '185.199.108.153' & '185.199.109.153' so I will simply create a DNS entry with my domain pointing to both IP address (A records). 

This load balancing is done by a type of DNS server called an 'authoritative nameserver' rather than using a dedicated piece of load balancing hardware. When the DNS authoritative nameserver is queried for an IP address, the server hands out a different address each time, operating on a rotation.

**Pros -**

- Super easy implementation
- Cheap 
- Marginally faster, since there's less routing and it operates at DNS level

**Cons -**
 
- If one of the server fails, DNS server will keep sending requests to it.
- It does not always provide evenly distributed load balancing due to both DNS cache and client side caching

> Some advance round robin services have methods to detect down server but there is still no work around for caching.

### Server side load balancing :

It acts as a "traffic police" sitting in front of web servers and routing client requests across all servers, capable of fulfilling those requests in a manner that maximises speed and capacity utilisation and ensures that no one server is overworked, which could degrade performance. If one server is down, it will redirect traffic to remaining servers and if a new server is added, it will automatically start sending requests to it.

There are several load balancing algorithms which provide different benefits. 

- Round robin
- Least Connections
- Least Response Time Method
- URL Hash
- Source IP hash
- Random with two choices
etc

Let's have a look at pros and cons.

**Pros -** 

- Reduced downtime
- Scalable
- Redundancy
- Flexibility
- Efficiency

**Cons -**

- Extra cost related to operating a dedicated load balancer 
- There is a limit to the number of requests the load balancer itself can handle. (However, this problem can be resolved with the combination of round-robin DNS and dedicated load balancers.)


### Client Side Load Balancing :

In client side load balancing, the client handles the load balancing. Let's take an abstract look at how this can be achieved. To perform load balancing on the client side -

- Client should be aware of all available web servers
- A library on the client side to implement load balancing algorithm

For letting client know about all available web servers, we can have 'servers.xml' stored on the client side -

```
<servers>   <server>s1.pankajtanwar.in</server>   <server>s2.pankajtanwar.in</server>   <server>s3.pankajtanwar.in</server>   <server>s4.pankajtanwar.in</server> </servers>
``` 

Now, based on chosen load balancing algorithm, client side code invokes one of the web server. The client has a preset timeout for each call and if the call takes greater then that, another server is selected.

**A major issue we can run into here is "Cross Site Scripting" -**

For security reasons, browsers will block calls to a different domain. For example, if the client wants to talk to the server s1.pankajtanwar.in, the client code must be loaded from the same domain, s1.pankajtanwar.in BUT we are on https://pankajtanwar.in

To over come this issue, we need to enable CORS to allow requests from '*.pankajtanwar.in'

**Advantages of Client side load balancer -**

- As client selects the server, the load should be distributed evenly
- Client has the ability to failover to another server when the chosen server does not respond in the preset time.
- It ensures that the cluster of servers appears to the end user as a single server
- No extra cost and resource required for a dedicated server side load balancer
- One less HOP, as each call routes through the server side load balancer implies an additional network hop, which in-turn implies additional latency.
- No need to spend time unnecessary, on configuring load-balancing hardware
- Another advantage is servers do not have to be housed in the same location and changing to another datacenter is as simple as making an update to the “servers.xml” file, instead of waiting for DNS changes to propagate.

**Is client side load balancing a good idea ?**

"Be careful" is all I have to say :)

although, Client side load balancing seems to be a good idea on paper but not a viable one in practice. While the arguments for balancing requests are good, cross scripting from a web browser is a very, very bad idea. Working around solutions that have been made for security reasons will most likely leave you wide open for other exploits that the JavaScript developer has not thought about.

I do agree that a load balancer and DNS round-robin might not be the best solution, but leveraging that with Javascript hacks is really the wrong way to go.

Client side load balancing can only be used for the very budget minded; don't use this if you're planning on having high-scalability and reliability though.

Client side resolution of services has been tried several times by several people, and it does not work, and is prone to lots of problems. In fact, a startup hoping to get big one day should never even try this since it is much more painful to grow with this approach.

Just a thought, but this approach can be used as a CDN (Content Delivery Network) to serve static files. (Host your website on a standard server but getting static content like images, css from another host)