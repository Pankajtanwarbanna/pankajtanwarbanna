---
title: Proxy vs Reverse Proxy - using a real life example!
date: '2021-03-24'
tags: ['system design']
draft: false
summary: I am sure, you must have heard of Proxy and reverse proxy server. This is a very important topic, when we talk about backend engineering & computer networking. Some times it's very confusing to find the exact difference between both of them.
author: the2ndfloorguy
---

I am sure, you must have heard of Proxy and reverse proxy server. This is a very important topic, when we talk about backend engineering & computer networking. Some times it's very confusing to find the exact difference between both of them.

I will start with a simple real life example to explain the concept of proxy in the simplest way possible.

### Analogy

In our childhood, when ever we wanted anything we always went to our parents and asked for it. And then it was upto parents to decide if it was a legitimate request to be fulfilled or not. 


![Storyboard Brainstorm Presentation (2).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1616433284797/9cNbuqDHE.png)

Suppose, you wanted a bat. You always thought that your parents are the one who will fullfil your request. That was actually true, as your parents went outside (to market) to get the stuff. They never let the outside world come in contact with you because your were too small. Thats how our parents shielded us from the outside world till the time we were matured enough to handle it by ourself.

Simply, just correlate this example with a proxy server. 

In simple words, proxy is an act that some one else does on your behalf. (Eg - Proxy attendance in college). Proxy is an interface between you and the outside world. 

### Proxy Server 


![proxy-server-1024x649.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1616432849097/C6AWCXVzO.png)

So basically proxy works for the safety of the client. Whenever we are making any request from our local machine to hit a server which is present outside the network (from intranet to internet), proxy it the interface through which we send the request and it fetches the data from the internet on our behalf.  

**Use cases of Proxy **

1. **Caching** - It is a very popular use case of proxy server. It saves bandwidth by caching the content and serving it back to the client instead of going to the internet everytime.

2. **Anonymity**  - Proxy talks to the server so the final destination (server) does not know from where the request is actually coming from.

3. **Security** - It acts as a firewall. Multiple client machines can talk to a centralised proxy server. It helps organisations and ISPs, block or filter the bad requests which could impact client machines sitting inside the network.

4. **Encryption & Decryption** - Proxy can encrypt and hide (mask) IP address and sensitive data going through the request. It only sends it in a way where you can not be discovered back.

5. **Logging** -  Logging can help caching the pages which are being visited often.

6. **Microservices** - This is one of the latest and very interesting use case of proxy. There are several types of proxy such as sidecar proxy, HTTP proxy, TCP proxy etc. We can deploy proxy next to our application and it can take care of all the networking stuff. 

For example, we are sending a HTTP request from our microservice and our sidecar proxy can upgrade it to a newer protocol as HTTP2 or HTTP3.

### Reverse Proxy Server 

As the name says, its just reverse of proxy and that's why it's very confusing. The main concept and the difference is that, instead of doing it from the client side (as it was in proxy), it does it from the server side. 


![FreeReverseProxy.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1616432861122/f2_Naj31I.jpeg)

It does the exact opposite of what a proxy server does. Instead of protecting the client, it protects the server. Reverse proxy server comes handy whenever there is any request coming from the internet, which wants to access any of our server within the network. So, instead of exposing our servers, every request goes through reverse proxy server which decides where to send this request forward. 

**Advantages of Reverse Proxy server -**

Almost all the benefits we have from proxy server including caching, security etc, all are there in reverse proxy too. Here we have some additional benefits. 

1. **Load balancing** - Reverse proxy helps balancing the load among the servers. Along with it, reverse proxy helps us compress (zipping) the request size which helps in increase of overall performance. 

2. **Ingress** - In kubernetes and microservice architecture, based on the request it funnels the request to the particular service. (eg. If requests comes for picture API, it takes the request to the respective service. Here it kinds of work as a router) 

3. **Canary Deployment** - Suppose, Youtube wants to experiment for 10% of requests to have a random generated thumbnail. This can be done by reverse proxy.

**IMPORTANT** 

**A pretty commonly asked question is what is the difference in a reverse proxy server and a load balancer?** 

A reverse proxy server can be a load balancer but load balancer has a very specific purpose of balancing the load only. Apart from it a reverse proxy servers can still work with a single server in place. It does not necessarily need to have multiple server because reverse proxy will still do the caching, security and other thing for that server. 

**Can proxy and reverse proxy be used at a same time?** 

Yes, there is a use case called "Service Mesh" where a proxy can also act as a reverse proxy at a same time. Service Mesh or a side car proxy is used in mircoservice architecture to handle timeouts, circuits breaks etc.

Thanks for reading.

About me : https://pankajtanwar.in

