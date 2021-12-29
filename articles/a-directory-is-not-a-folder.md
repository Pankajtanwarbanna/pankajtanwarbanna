---
title: A directory is not a folder.
date: '2021-12-29'
tags: ['linux']
draft: false
summary: I started using computers with Windows and later switched to Linux. I didn't even realize my transition from using the term folder to directory. Yesterday, my crooked mind tricked me to research why folders are called directories in Linux.
author: the2ndfloorguy
---

I started using computers with Windows and later switched to Linux. I didn't even realize my transition from using the term `folder` to `directory`. 

Yesterday, my crooked mind tricked me to research why folders are called directories in Linux.

I didn't exactly know the difference between these two words in English before. Good souls on the internet had some really good explanations on it.

## Folder vs Directory.

A folder (like a file folder) is a space, used to hold several files BUT a directory (like a telephone directory) is like an index that points to the location of items inside it.

![Folders vs Directories](https://user-images.githubusercontent.com/25531072/147690782-2e9b2a8a-743c-4a93-ba4d-012fb8a7219e.png)

If we understand this with an analogy, a folder is like a hotel with rooms inside it but the directory is a hotel keeper, who knows all the rooms.

It's the right time to admit the fact that I am super dumb. I used to think, a directory in Linux, keeps the actual files inside it. 

> A directory is just a `special file` that has the locations of the files inside it.

So, this makes sense why it's called directory. Somewhere, in my mind, it was connecting the dots to the famous phrase "Everything is a file on Linux".

Anyway, TLDR is, it all depends on the perspective. `Directory` is a file system concept. And when you are in a graphical environment that has visualization, people prefer to use the term `folder` as it's a GUI object.

Well, to overcome this guilt of being so dumb, I decided to explore the typical Linux file system internals a bit to understand how the directory is actually structured.

## Directory in Linux
So, a Linux file system has two major components, a pool of data blocks where the data is stored and a database system to manage this pool.

More specifically, a directory is a file that has a list of objects (file name and its i-node number). 

So, Inode is something new here. Don't worry!

Inode stands for Index Node. It is a data structure, which stores metadata for every file on the system in the i-node table. The Inode contains a wealth of information like ownership, permissions, file type, size, timestamp, data block address, etc (so everything except the file name and the actual file data).

Every file has one 1 inode. Directories, char files, block devices, are all files and they each have 1 inode.

So, whenever a new file is created, an inode number and a file name are assigned to it. And, both inode number and file name are stored as entries in the directory of that file.

> Inode numbers are unique at the partition level and every new file is given a unique inode number.

Now, let me tell you something that may surprise you...

If you run out of inodes, you can not create new files even if you have space left on the system. So next time, when your Linux system complains that no space is left even after having more than enough, then you know who is the culprit. 

> Some people, dealing with mail servers have raised the issue of running out of inodes way before running out of space because mail servers usually store a big amount of small-small files.

So now, that I end my ramblings, I hope you have learned something new.