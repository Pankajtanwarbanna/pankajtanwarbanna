---
title: Possible ways to iterate a string in JavaScript.
date: '2021-01-10'
tags: ['javaScript']
draft: false
summary: JavaScript is a commonly used language. Let's see some really intresting methods to iterate a string in JavaScript.
author: the2ndfloorguy
---

First, we need to understand the basic ways to get the **char** at a particular index in the string. There are two ways basically. 

**str.chatAt(index)**
- charAt() method returns a new string, at particular given index.
- Supported in Old browsers (IE7 too)
- If no index passed, takes index, by default 0
- If out of range index, returns empty string

**Bracket Notation (str[index])**
- Bracket method returns char at particular given index.
- It was introduced in ECMAScript 2015 but breaks in old browsers
- If no index passed, throws syntax error
- If out of range index, returns undefined

Iterating over the string -

1. **For Loop** 
```
 const str = "pankaj";
for (let index = 0; index < str.length; index ++) {
           console.log(str[index]) // str.charAt(index)
} 
``` 

2. **for..of**
```
const str = "pankaj";
for (let c of str ) {
        console.log(c);
} 
``` 

3. **forEach()**
```
 const str = "pankaj";
[...str].forEach( (c) => console.log(c) ); 
``` 

**BONUS**  => Downside of bracket notation is that, it cant be used to assign a character 

```
let str = "pankaj"
str[0] = "t"
console.log(str) // "pankaj" 
``` 