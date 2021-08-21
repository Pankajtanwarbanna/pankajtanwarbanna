---
title: Advantages & disadvantages of declaring JavaScript functions inside objects.
date: '2021-01-10'
tags: ['javaScript']
draft: false
summary: Few days back, one of my friends was asked this question in his JS developer interview. There are several methods & coding techniques to write JavaScript functions.
author: the2ndfloorguy
---

Few days back, one of my friends was asked this question in his JS developer interview. There are several methods & coding techniques to write JavaScript functions. The most common approach is -

- **Method 1**

```
function foo() {
      // do something 
}

function bar() {
      // do something 
}
``` 

In interview, he was given a code similer to this pattern.

- **Method 2**


```
let my = {
  name : "Pankaj",
  foo  : function() {
      // do something
  },
  bar  : function() {
      // do something
  }
}
my.foo();
my.bar();
``` 

From the top level view, we do not find any specific difference in terms of code readability or code execution time. But if we deep dive a bit we can see that -

**Advantages :**

- The real advantage of using second method(functions inside object) is that we are not polluting the global namespace with some extra functions means in first code snippet, we have 2 variables declared in global namespace and in second method we have only one. As we are not polluting the global namespace, global variable "foo" declared anywhere else will not hide our "foo" function inside "my" object.

- An another advantage we can say that, now all related functionalities are grouped together so it makes our code little bit more cleaner, readable & easy to understand.

**Disadvantages :**

- In second method, calling a function is slightly longer (eg : **my.foo()**) and if we have bunch of extra nested functions, it would be more longer

```
my.today.pankaj.getName()
``` 

- Second method requires some extra processing by JS engine but this will not affect the speed much as it would be like optimizing our code execution time ~0.1ms , not an achievement).