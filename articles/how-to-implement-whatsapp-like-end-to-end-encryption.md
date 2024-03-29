---
title: How to implement WhatsApp like End-to-end encryption?
date: '2021-04-29'
tags: ['encryption']
draft: false
summary: One of the WhatsApp key feature is end to end encryption of messages. In this article, we will quickly understand a simple implementation of WhatsApp like end to end message encryption.
author: the2ndfloorguy
---

One of the WhatsApp key feature is end to end encryption of messages. In this article, we will quickly understand a simple implementation of WhatsApp like end to end message encryption.

### What is End to end encryption 🔒

If we take example of WhatsApp, the concept of end to end encryption is that a message sent from user A to user B over WhatsApp, can only be read by user B, no one else can read that in between, not even WhatsApp servers. 

> The e2e encryption implementation which we are going to discuss here is not how exactly WhatsApp or signal app does, It's is just to give you a brief idea of, how we could do that! 

First, we will go through the typical chatting application architectures implementation to understand their drawbacks.

### Implementation 1


![Untitled Diagram.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619621026734/I_0YPD-uG.png)

Raw text is sent via HTTP request to the server, it is saved in the DB and sent to User B via HTTP.

**Pros**

1. Super simple to implement

**Cons**

1. No end to end encryption

2. Content is being sent in plain text format. Highly vulnerable to **man in the middle** attack. An attacker can sniff into the network and see the content or worse can alter it.

3. Content is saved in plain text on servers. If servers are hacked, your data can be compromised.

### Implementation 2


![Untitled Diagram (1).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619621090530/_d3BQZ8MZ.png)

Raw text is transferred over HTTPS to the server.

**Pros** 

1. Man in the middle attack is not possible as we are transferring data using HTTPS so TLS will encrypt the data over the network.

**Cons**

1. TLS termination happens at load balancer level and data will be decrypted at backend server and servers can read your messages as data is saved in plain text.


### Implementation 3 

To implement end to end encryption, we use a method called **Public key cryptography**.

In Public key cryptography, every user has two keys, Public key and private key. As their names suggest, one user's public key is visible/accessible to all other users and private key is very private to the user. It is saved locally on the device and can be accessed by self, not even by backend servers.

The concept of public key cryptography technique is -

1. Data, encrypted using any user's public key, can only be decrypted by the same users' private key.
2. Data, encrypted using any user's private key, can be verified by the same users' public key

For example, if any message is encrypted using public key of user A, then it can ONLY be decrypted by private key of user A. and if any message is encrypted with private key of user A, it can be verified by public key of user A.

> The very important thing of note here is any message encrypted using public key of user A, can not be decrypted by same public key. It can only be decrypted by private key of same user.

We will use this technique for our end to end encryption implementation.

let's say A wants to send a message to B.

- We have access to public key of user B.
- So, we will encrypt the message with public key of user B.
- Now, we know that it can only be decrypted with private key of user B and only B has access to his private key.

So, this is how we can ensure that not even backend server can decrypt and read the message.


![end-to-end-encryption-1024x550.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1619621206396/EyoH-ofYp.png)


One more important thing, when message is received by user B from user A, there must be a method for user B to verify that this message was actually sent by user A (as everyone in the network has access to public key of user B, any one can send the message to him).

To ensure it, we  use a "Digital signature".

Here we use, second concept of public key cryptography.

- When user A wants to send a message to user B, along with the message, he adds a tiny Digital signature encrypted by his private key. 
- When user B receives the message, he can verify the digital signature with public key of user A to make sure the message was actually sent by user A.  

Hope, it gave you a fair idea of how we can implement end to end encryption.

✍️ **Take Home Assignment** : How can we implement End to End Group Messaging encryption using public key cryptography? Let me know in the comments! 

> References : [Public Key Cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography), [Public and Private Keys](https://www.preveil.com/blog/public-and-private-key/), [Arpit Bhayani](https://www.youtube.com/watch?v=m_7xSIhxZL8&ab_channel=ArpitBhayani)