---
title: Jersey CTF III - poisoned
date: 2023-04-16 23:19:54
tags: writeups
---

```
Seems these pesky AI hackers are up to no good again! 
You must find out how where they POISONED this site and 
use that to find the file they placed on our web server!
```

Right away you can tell that this challenge wants you to manipulate the php parameter page based on the URL

```
https://jerseyctf-poisoned-v2.chals.io/?page=welcome
```

If you throw an arbitrary string there, you can see that based on the error it's likely possible to inject something there to read off the server. 

```
https://jerseyctf-poisoned-v2.chals.io/?page=../../../etc/passwd
```

The above LFI doesn't work because the server is attempting to sanitize our input but they dropped the ball and likely only had code to remove the "../", because the following LFI works:

```
https://jerseyctf-poisoned-v2.chals.io/?page=....//....//....//....//etc/passwd
```

Based on the challenge name and the description, we should look at either the apache or nginx logs to see if the other hackers poisoned the log files. I won't show the nginx file since it turned up empty but the apache2 logs seem like they've been poisoned.

```
https://jerseyctf-poisoned-v2.chals.io/?page=....//....//....//....//var/log/apache2/access.log
```

![](poisoned1.png)

The php script put at access.log is expecting a parameter `command` which will get run by `system()`. The rest of the challenge is trivial and we can either list around the server or get a reverse shell to explore on our own.
