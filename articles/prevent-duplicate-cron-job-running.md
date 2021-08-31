---
title: Prevent duplicate cron job running.
date: '2021-08-31'
tags: ['notes']
draft: false
summary: Today, while working on an in-house project, I encountered a really interesting concurrency problem. I have scheduled a cron job to run every 30 minutes, but if the script takes more than 30 minutes to finish, another cron job started and these jobs started stacking up over each other. This essay discusses the approach to solve get rid of this interesting issue.
author: the2ndfloorguy
---

Today, while working on an in-house project, I encountered a really interesting problem. I needed a python script, running every 30 minutes, pulling some information from a third party, processing the data, updating on my local database & take a rest till the next round. I wrote the script and set-up the cron.

Smooth right!

But, my happiness didn't last long. Sometimes, my script took more than 30 minutes to execute. This presented me with a beautiful issue of cron jobs overlapping & data duplication. I didn't want the jobs to start stacking up over each other.

Ahh. Cute [concurrency](https://en.wikipedia.org/wiki/Concurrency_(computer_science)) problem.

To fix this, like any other developer, a couple of thoughts popped up in my mind. 

- Modify my python script, use some internal package to list down all running processes & grep if the same cron is already running. If yes, maybe it's not a good time to run it.
- Why not look for the existence of a particular file `mylock.txt` and exit if it exists or create it if it doesn't?

Both solutions seemed pretty lousy & unsafe. And touching a working code is my biggest nightmare.

Our internal discussion, headed me over to a beautiful tool, [Flock](https://linux.die.net/man/1/flock). 

# So, What's flock?

[Flock](https://linux.die.net/man/1/flock) is a very easy & simple tool. This tiny utility comes by default with the `util-linux` package.

Its mechanism is pretty neat and simple. For execution, it takes a `lock file` & `command` to run as input. It puts a lock on a given lock file and releases the lock when the script is executed. Lock on the file helps the tool decide, whether to run the script or not, in the next round. 

> Just to add here, [file locking](https://en.wikipedia.org/wiki/File_locking) is a mechanism to restrict access to a file among multiple processes. It allows only one process to access the file at a specific time.

## How to setup a cron job, using flock ?

Setting up a cron using flock is pretty simple. 

### Step 1 - Install flock, if not available in your system

```
yum install -y util-linux
```

> You can verify if flock has been installed by `whereis flock` in linux system. It should show `/usr/bin/flock` as a path.

### Step 2 - Open up cron tab

```
crontab -e
```

### Step 3 - Install your new cron 

```
 */30 * * * * /usr/bin/flock -w 0 /home/myfolder/my-file.lock python my_script.py
```

And you are done.

The moment flock starts, it locks the `my-file.lock` file & if in next round, the previous cron is already running, it will not the script again.

> Don't worry about `my-file.lock` , flock will create it for you if it doesn't exist.

To verify the lock, try -

```
fuser -v /home/myfolder/my-file.lock
```

So, my crontab entry looked like this -

```
 */30 * * * * /usr/bin/flock -w 0 /home/myfolder/my-file.lock python my_script.py > /home/myfolder/mylog.log 2>&1
```

Well, calm down. I know, I have added some random texts to my cron. Let's decode the meaning of `>/home/myfolder/mylog.log 2>&1` one by one.

- `>` is standard I/O redirection.
- `/home/myfolder/mylog.log` is a black hole where any data is sent
- `2` is the file descriptor for standard error (STDERR)
- `>` , again for redirect
- `&` symbol for file descriptor 
- `1` is file descriptor for standard output (STDOUT)

`2>&1` means a redirection of channel 2 (STDERR) to channel 1 (STDOUT) so both outputs are now on the same channel 1. `>/home/myfolder/mylog.log` means, output from channel 1 will be sent to this black hole. 

To sum it up, output & errors are generated while the execution of your script will go to this file.

## The unfortunate truths of flock

### How to run multiple commands with flock

I had an interesting use case. Due to some system absolute path-related stuff inside my python script, I had to run the script as a combination of two commands. 

Instead of -

```
python /home/myfolder/script.py
```

I needed to do - 

```
cd /home/myfolder/ && python script.py
```

Running multiple commands with the help of flock, is a bit tricky. After a bit of struggle, this one worked for me.

```
*/30 * * * * cd /home/myfolder/ /usr/bin/flock -w 0 /home/myfolder/my-file.lock && python my_script.py > /home/myfolder/mylog.log 2>&1
```

### flock doesn't always seem to be working.

Flock does [advisory locking](https://www.baeldung.com/linux/file-locking#1-advisory-locking), which is a cooperative locking scheme which means you will be able to override the lock if you don't cooperate. 

It has been raised many times that if, the flock is used to invoke a command in a subshell, other programs seem to be able to read/write to the locked file. [This issue on stackoverflow](https://unix.stackexchange.com/questions/388026/flock-doesnt-seem-to-be-working) talks about this in detail. 