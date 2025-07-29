---
title: Jersey CTF III - avenge-my-password
date: 2023-04-17 10:03:43
tags: writeups
---

```
Stark to Star-Lord - "Oh my, see Quill?! I told you your credentials can work for more 
than one thing, but things only go so far. Time will tell when to search another path 
to find a way in!" Automated directory brute force is unnecessary.
```

This was my favorite of the web challenges from this year's Jersey CTF. There's still a bit of a CTF element but it combines a few different skils that are very applicable in the real world whether you're a penetration tester or pivoting as a red teamer. 

```
quantumleaper@avenge-my-password:~$ cat .mysql_history
_HiStOrY_V2_
show\040databases;
use\040information_schema;
show\040tables;
show\040databases;
show\040website;
use\040website;
show\040tables;
CREATE\040TABLE\040login\040(
\040\040\040\040\040\040\040\040userID\040INTEGER\040NOT\040NULL,
\040\040\040\040\040\040\040\040email\040VARCHAR(128)\040NOT\040NULL,
\040\040\040\040\040\040\040\040PRIMARY\040KEY\040(userID)
);
select\040*\040from\040login;
INSERT\040INTO\040login\040VALUES\040(5000,\040'lucy.hatfield@elmatrix.com',\040'75a9d701d4b530645c35c277ba6bf0ef');
```

Immediately upon login we find a log history file for mysql, so we know there's a "website" database running that's storing login information. We confirm there's definitely a website on the server by checking `/var/www/html` which contains a php file and an interesting list of folders including one called ".username" which has a file ".usernames.txt" which we are unable to read due to the access control explicitly blacklisting our user from reading it (note the + at the end of the permissions lines).

```
quantumleaper@avenge-my-password:/var/www/html$ cd .username/
quantumleaper@avenge-my-password:/var/www/html/.username$ ls -la
total 16
dr-xr-xr-x   2 root root 4096 Mar 23 19:38 .
