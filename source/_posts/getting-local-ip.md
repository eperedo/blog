---
title: Getting my local ip
date: 2018-02-08 22:58:00
---

Sometimes I need to know my local IP address, so easy, right?

```bash
ifconfig
```

The problem with the output of **ifconfig** is that is too much information and I only want my damn local IP.
Yes! I am making drama because there is no - or worst because I do not know - command who print just my local ip.

Since I like to waste time in such a useless tasks I wrote an alias to get what I want and this is the result.

```bash
myip='print '\''wifi:'\''; ipconfig getifaddr en1;print '\''cable: '\'';  ipconfig getifaddr en0'
```

You are welcome! 🤓
