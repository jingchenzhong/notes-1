---
tags: [ cs ] 
title: IO and events
date: 2017-04-04T20:08:35+08:00 
category: cs
---

# Reference
[What is the status of POSIX asynchronous I/O AIO](http://stackoverflow.com/questions/87892/what-is-the-status-of-posix-asynchronous-i-o-aio)
[hat's the difference between event-driven and asynchronous? Between epoll and AIO?](http://stackoverflow.com/questions/5844955/whats-the-difference-between-event-driven-and-asynchronous-between-epoll-and-a)
[Asynchronous I/O and event notification on linux](http://davmac.org/davpage/linux/async-io.html)
[Gnu Libc Perform I/O Operations in Parallel](https://www.gnu.org/software/libc/manual/html_node/Asynchronous-I_002fO.html)

# Really confuing conceptions
What is Asynchronous I/O?
The onset of I/O is splitted from nuclus.
First, asynchronous I/O can be happned in UP machine.
AIO is based on Proactor, more portable.

# Aynchoronous event notfication
epoll is based on Reactor pattern.
signal is based on Observer pattern.
For the ease of use, the select loop is implemented as an *event loop* with callbacks.
libevent and libev is a well-designed *event loop*.Check shadowsocks for using of libev.

# FAQ
memory-map IO

