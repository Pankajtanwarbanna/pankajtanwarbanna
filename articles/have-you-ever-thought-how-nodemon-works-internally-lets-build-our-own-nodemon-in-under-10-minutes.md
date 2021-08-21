---
title: Have you ever thought, how ‘nodemon’ works internally? Let’s build our own ‘nodemon’ in under 10 minutes!
date: '2021-05-30'
tags: ['nodemon', 'javascript']
draft: false
summary: If you have ever worked with Node.Js, you must have used a package called nodemon for development.But, have you ever thought about how it works internally? How would you proceed if you are asked to build a nodemon clone?
author: the2ndfloorguy
---

Hey 👋,

If you have ever worked with Node.Js, you must have used a package called `nodemon` for development. 

> For those who are not aware of it, [nodemon](https://www.npmjs.com/package/nodemon) is a tool that helps develop NodeJs based applications by automatically restarting the node application when file changes in the directory are detected. We can simply install it globally and use it throughout our system without making any additional changes to the code.

But, have you ever thought about how it works internally? How would you proceed if you are asked to build a nodemon clone?

This is a really interesting Node and JavaScript developer interview question. It helps the interviewer to test your basics such as NodeJs stream, child process, events, debouncing etc. 

> Github Repo Link - [Nodekeeper](https://github.com/Pankajtanwarbanna/nodekeeper)

In this article, we will create a simple Node.Js CLI (command line application) tool named as `nodekeeper`, similar to `nodemon`. So, let’s get started. 

### Requirements 

- We should be able to run any JS file using `nodekeeper <filename>` command
- Automatically restart the node application when changes are detected in files
- Manually restart the server when user enters `rs`


On a high level, the problem might seem very difficult to implement but it’s not. The main idea behind it is to create a CLI tool which will create a node child process for the given file and keep eyes on the files in the repo. If new changes are detected, just kill the child process and again create a new process.

Ok, some of the terms might seem very technical. Let’s get more into details. 

### Let’s first understand how to create a NodeJs CLI tool.

So first, what is a CLI tool? CLI stands for ‘command line application’. It helps us run any command on the terminal which will do some magic with our system. For example - to run any JavaScript file NodeJs provides us with `node` CLI. We just have `node index.js` from the command line(terminal) and it executes the file. We can give commands just from the terminal.

In our use case also, we want to run a JavaScript file using `nodekeeper index.js`. 

Let’s start. First, we create a new folder named `nodekeeper` and do `npm init` inside it to set up the node project. 

```
mkdir nodekeeper 
cd nodekeeper
npm init
```

After it, a package.json file would be generated. Which will look something like this -

```
{
	"name": "nodekeeper",
	"version": "1.0.0",
	"description": "",
	"main": "index.js",
	"scripts": {
		"test": "echo \"Error: no test specified\" && exit 1"
	},
	"keywords": [],
	"author": "Pankaj Tanwar",
	"license": "ISC",
	"dependencies": {
	}
}
```

Let’s create a new javascript file `index.js` and paste the following code. 

```
#!/usr/bin/env node
console.log(‘Hey! Welcome to nodekeeper’);
```

Here the first line which starts with **#!** is called ‘shebang’. Its main purpose is to inform the system what type of script is included in the rest of the file. Here, we have included a path to node binary which tells the system that our file is a file which can we executed by node. 

To run a JavaScript file using `nodekeeper index.js` instead of `node index.js` we need to create a duplicate node binary. 

For this, we add a new key “bin” inside our package.json file.

```
{
	"name": "nodekeeper",
	"version": "1.0.0",
	"description": "A lightweight alertnative to nodemon.",
	"main": "index.js",
	"scripts": {
		"test": "echo \"Error: no test specified\" && exit 1"
	},
	"bin": {
		"nodekeeper": "./index.js"
	},
	"keywords": [],
	"author": "Pankaj Tanwar",
	"license": "ISC",
	"dependencies": {
	}
}
```

Now, to install this tool to run globally in our system we run -

```
npm link
```

Now, anywhere in our system we can run any JavaScript file like this ‘nodekeeper <filename>’. Really cool right?

Let’s understand now, what child processes are. As we all know, NodeJs is single threaded but still we can take advantage of the child processes with the help of the `child_process` module. To scale our node app, It helps us to leverage parallel processing on multi-core CPUs. 

In simple terms, a child process allows us to run any system command. 

### Let’s understand child process with an analogy

Today, my father was working on something and he wanted to drink water. As I was sitting doing nothing (as usual), so, he asked me to bring a glass of water for him. Here, my father is the main process who is performing some work. He could also go and get the glass of water but it would impact his work so he has a child process (which is me), and assigned a task to it. This is called parallel computing. Now, my father can continue working on his task and when I (child process) completes my task, I will let the main process know.  

![Father Child Analogy](https://cdn.hashnode.com/res/hashnode/image/upload/v1622352428805/xup9EMtvX.png)

So, when we want to run a JavaScript, in the terminal we run `node index.js` and we get the output. In a similar way, we can create a child process and tell it to run the `node index.js` command, and give us the output. 

There are 4 ways to create a child process in Node.Js, spawn(), fork(), exec() and execFile(). For running a system command, spawn() and exec() are useful. 

### Syntax for spawn() 

```
const spawn = require(‘child_process’).spawn;
let nodeServer = spawn(‘node’ , [ ‘index.js‘ ])
```

### Syntax for exec() 
```
const exec = require(‘child_process’).exec;
let nodeServer = exec(‘node index.js’, function(data) {
	console.log(data);
})
```

Both of them will run `node index.js` command on the terminal. To view the output, we need to pipe this child process to the main process. To do so, 

```
let nodeServer = spawn(‘node’ , [ ‘index.js’ ], { stdio: [ process.stdin, process.stdout, process.stderr ]})
```

It has piped the child process to the main process. So, we can log it’s output on the terminal.

**BUT here is a catch in the working of spawn() and exec(). **

spawn() gives output in streams but exec() gives out after the whole data is received. Suppose, in the file index.js we have -

```
console.log(‘Hey Hashnode’)
setTimeout(function() {
	console.log(‘Timer completed’);
}, 5000)
```

If we run this file using both methods. We see that spawn child process, immediately logs ‘Hey Hashnode’ and after 5 seconds it logs ‘Timer completed’ but exec child process will log both lines after 5 seconds. So, it explains spawn gives output in streams, it does not wait for the file to execute completely. 

For our use case, we need to spawn the child process.  

For watching files to new changes, we can make use of NodeJs inbuilt module, **fs**. It exposes a function called **fs.watchFile** but there have been a lot of issues reported by the community saying it’s not reliable. It fires multiple events sometimes for a single file change which results in high CPU utilization. So, to overcome this problem we can use the [chokidar](https://www.npmjs.com/package/chokidar) package. 

We can pass in watch paths and other paths, we want to be ignored and we can listen to it’s events to get notified when there is a new change.

```
const chokidar = require(‘chokidar’);

chokidar.watch([
	"/**/*/*js"
], {
	ignored : “**/node_modules/**”
}).on(‘all’, () => {
	console.log(‘File changes detected’);
)
```

So, whenever we detect changes, we can kill the current node child process and start a new process again. 

To kill a process -
```
nodeServer.kill(‘SIGTERM’) 
```

SIGTERM is just one of the signals which is used to kill any process. There are many types of signals. More Info can we found [here](https://nodejs.org/api/process.html#process_process_kill_pid_signal)

If we structure our code a bit, our final index.js for this would look like this -

```
#!/usr/bin/env node
const spawn         = require('child_process').spawn;
const chokidar      = require('chokidar');
const path          = require('path');

class Nodekeeper {
    constructor() {
        this.__init__();
    }

    __init__        = () => {
        this.args               = process.argv;
        this.fileName           = this.args[2];
        this.cwd                = process.cwd();
        this.watchPaths         = [
            path.join(this.cwd, "/**/*.js")
        ];
        this.ignoredPaths       = "**/node_modules/*";

        this.reload();
        this.startWatching();
        this.listeningEvents();
    }

    reload          = () => {
        if(this.nodeServer) this.nodeServer.kill('SIGTERM');

        this.nodeServer     = spawn('node', [ this.fileName ], { stdio: [ process.stdin, process.stdout, process.stderr ]});
    }

    startWatching   = () => {
        chokidar.watch(this.watchPaths, {
            ignored         : this.ignoredPaths,
            ignoreInitial   : true
        }).on('all', (event, path) => {
            this.reload();
        });
    }

    listeningEvents    = () => {
        // listening on CLI input
        process.stdin.on("data", (chunk) => {
            let cliInput = chunk.toString();

            switch(cliInput) {
                case 'rs\n':
                    this.reload();
                    break
            }
        });
    }
}

new Nodekeeper();
```

Now, we can see, if we create a new express server, go to that folder and run it using `nodekeeper server.js` , upon file changes, it will automatically restart the server. 

We put everything in a `nodekeeper` class and export it as a module. 

We have one more requirement which is when a user enters `rs`, we need to manually restart the server. It’s very simple as we have already implemented logic for restarting the server. To capture what user entered we just need to put an event on the main process. 

```
process.stdin.on("data", (chunk) => {
    let cliInput = chunk.toString();

   switch(cliInput) {
        case 'rs\n':
             this.reload();
             break;
   }
});
```

It works great but there are still some issues. 

- What if we save multiple files at the same time or we press Ctrl+S multiple times. Chokidar would fire change events multiple times. So, it will kill the on-going node process and start a new one which is CPU extensive. To overcome this problem, we use a concept called ‘debounce’. We delay the execution for a time period and execute it once the user stops saving. (This concept is used in search bar suggestions, it will not fetch data on every keystroke, it will affect the performance, Instead of it, we typically fetch the data when user stops typing)

Hope you have a got a decent idea of what how to proceed to build a nodemon clone.

I have published with a modified version `nodekeeper` - a lightweight nodemon alternative. Package can be found [here](https://github.com/Pankajtanwarbanna/nodekeeper). If you are willing to contribute, pull requests are welcome. 

**References -**

- [https://nodejs.org/](https://nodejs.org/)

Special Thanks to [Bhumij Gupta](https://twitter.com/bhumijgupta)
