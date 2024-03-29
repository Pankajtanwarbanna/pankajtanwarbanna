---
title: Do you know 0.1 + 0.2 ≠ 0.3 in JavaScript? Here is why!
date: '2021-05-21'
tags: ['javaccript']
draft: false
summary: It’s been a while since I have been working with JavaScript. Yesterday, I experienced a very strange behaviour - console.log(0.2 + 0.1) // 0.30000000000000004
author: the2ndfloorguy
---

Hey 👋,

It’s been a while since I have been working with JavaScript. Yesterday, I experienced a very strange behaviour.

![JavaScript](https://cdn.hashnode.com/res/hashnode/image/upload/v1621527664759/jRzoH1cE-.png)

I was really confused & surprised at the same time 😕 . Initially I thought, I have found an argument to curse JavaScript again.

But, after doing a bit of research, I figured out that this was not an error. This is math and the way computers deal with numbers.

Well, there are some other weird stuff too -


![JavaScript Weird Stuff](https://cdn.hashnode.com/res/hashnode/image/upload/v1621527986757/OBw4DSiR7.png)

### What’s happening behind the scene?

The simple logic behind it is computers use a **base-2 (binary) floating numbers system**. 

Let’s understand it in detail with a very simple analogy. 

We humans use the **base-10 (decimal) number system** to read, write and understand numbers in daily life. 

When someone tells us 1/10, we understand and take it as 0.1 but for 1/3, we don’t have an exact value. It’s 0.333333….. (never ending) so for our understanding, we take it as 0.3 (nearest possible complete number).

The same case is with computers. When in JavaScript, we write -


```
let a = 0.1;
``` 

As we know, computers stores in binary format so this number is converted to binary but every possible real number can’t be converted to binary. As we can see, there is no exact binary representation possible for 0.1 so JavaScript interprets 0.1 as the closest possible binary number in decimal which is -

```
0.1000000000000000055511151231257827021181583404541015625
```

In binary -

```
0.0001100110011001100110011001100110011001100110011001101
```

In same way, 0.2 is interpreted as -

```
0.200000000000000011102230246251565404236316680908203125
```

So when we do 0.1 + 0.2, 

```
0.1000000000000000055511151231257827021181583404541015625
+
0.200000000000000011102230246251565404236316680908203125
=
0.3000000000000000166533453693773481063544750213623046875
```

The really interesting thing is, even this output also is converted to the nearest possible binary number because JavaScript numbers can’t represent this precise result either and in decimal which is -

```
0.3000000000000000444089209850062616169452667236328125
```

Now, we need to understand what happens when we console log this value.

JavaScript does not log every last decimal place of a number. Instead, JavaScript only logs out the minimum number of digits necessary to uniquely identify that JavaScript number from the other JavaScript numbers near it.

So, for example we log the value -

```
console.log(0.1000000000000000055511151231257827021181583404541015625)
// 0.1
```

Output is 0.1

That’s why, we try to log the result of 0.1 + 0.2, 

as we have already concluded the result which is -  
```
0.3000000000000000444089209850062616169452667236328125
```
So, JavaScript logs out the minimum number of digits to uniquely identify that number which is -
```
0.30000000000000004
```
So, this is why the answer of 0.1 + 0.2 is 0.30000000000000004

The very important thing to notice here is that the JavaScript number data type is not actually a number, it’s  [IEEE 754 double-precision (64-bit) floating-point numbers](https://en.wikipedia.org/wiki/Double-precision_floating-point_format) , or "doubles". 

This behaviour is not specific to JavaScript only. It has been seen in other language too. For more details -  [https://0.30000000000000004.com/](https://0.30000000000000004.com/) 

I hope, now you have got a fair idea of what to say when someone asks why 0.1 + 0.2 is not 0.3 