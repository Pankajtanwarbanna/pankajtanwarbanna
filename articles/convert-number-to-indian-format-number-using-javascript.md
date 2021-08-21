---
title: Convert number to Indian format number using Javascript.
date: '2021-01-12'
tags: ['javascript']
draft: false
summary: Different countries have different conventions to display monetary values. Likewise, India follow Indian numbering convention. Let's checkout quickly, how to convert number to indian format number.
author: the2ndfloorguy
---

Different countries have different conventions to display monetary values. Likewise, India follow Indian numbering convention.

**Example** 

> 
12345678 // normal number

> 
1,23,45,678 // number in Indian number convention

There are several simple and clean ways to convert a number to Indian number format in Javascript without downloading any external library.

- **using toLocalString() -**

This is the simplest way to format a number to Indian number system.


```
let number = 12345678
console.log((number).toLocaleString('en-IN'))  // 1,23,45,678
``` 

Here, number can be passed as string as well.

-  **Using regex expression**


```
let number = "12345678"
number = number.replace(/(\d)(?=(\d\d)+\d$)/g, "$1,") // 1,23,45,678
``` 

for number, having decimal values, this will not work. For such number, a minor modification will do the job.


```
let number = "12345678"
number = number.split(".");
console.log(number[0].replace(/(\d)(?=(\d\d)+\d$)/g, "$1,") + (number[1] ? ("."+number[1]): ""));
``` 


This regex expression matches the pattern and adds comma.

- **Intl - Internationalization API**

```
let number = "12345678"
console.log(Intl.NumberFormat('en-IN').format(number)) // 1,23,45,678
``` 


- **Simple logic -**


```
let value = "12345678";
let separator = 3;
let visit = 0;
let number = '';
for(let index = value.length - 1;index >= 0; index --) {
  visit += 1;
  number += value[index];
  if(visit === separator && index !== 0) {
    number += ','
    separator = 2;
    visit = 0;
  }
}
console.log(number.split("").reverse().join(""))  // 1,23,45,678
``` 



