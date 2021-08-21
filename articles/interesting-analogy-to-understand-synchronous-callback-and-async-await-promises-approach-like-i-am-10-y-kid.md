---
title: Interesting analogy to understand synchronous, callback, and async-await (promises) approach like I am 10 Y Kid 🧒
date: '2021-04-23'
tags: ['javascript']
draft: false
summary: Let's quickly understand the difference between synchronous, callback, and async-await (promises) approach like a 10 Year old Kid.
author: the2ndfloorguy
---

Let's quickly understand the difference between synchronous, callback, and async-await (promises) approach like a 10 Year old Kid.
 
1. My friend was moving to Surat, so he invited me to a party and some shopping. We went to a restaurant. It was Sunday so the restaurant was full. We talked to the manager, he noted down our names, gave us a token number, and asked us to wait🥺.

2. It was taking so much time so I decided to try some other restaurants. Again, no table available BUT their approach was different. They took our phone no and asked to do our shopping work and when a table was available, they would give us a call so that we didn't have to wait. A win-win for both. After shopping we had our dinner. Perfect!!

3. But after a few days, we started getting offer discount SMS from the restaurant. Oops!!! The next time, we clearly refused to give them our contact no, so they gave us a buzzer which would ring when a table was empty and told us to go shopping. They have nothing from us now, even it was up to us whether we want to go or not when the buzzer rings.

So,

**Situation 1** : Synchronous ( Blocking approach ) - Not efficient

**Situation 2** : Giving contact no means leaving a callback, non-blocking but not that efficient as no control over callback (SPAM MSGS), callback hell (nested callbacks)

**Situation 3** : async-await (wrapped in promise) - asynchronous, non-blocking, easy and clean, No callback only two args - resolve and reject, efficient BUT messy error handling

<hr>

Thanks for reading!

Give me cheeky follow on [twitter](https://twitter.com/the2ndfloorguy)