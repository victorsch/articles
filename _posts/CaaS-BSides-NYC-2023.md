---
title: CaaS - BSides NYC 2023
date: 2023-04-23 23:05:19
tags: writeups
---

```
We are bringing capitalism to the cloud with Calculator as a service (CAAS). Try our trial version today!
```

This was a fun challenge from BSides NYC 2023 created by Include Security. The premise of the application is a calculator service that continuously loops through and allows us to perform calculations to a limited extent. If you play around with the calculator, you'll notice some funny bugs where the sum can't exceed 255 but this ends up having nothing to do with the exploit. 

```
~/bsidesnyc/includesecurity.com/caas  file caas
caas: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, BuildID[sha1]=9906a441e40c6c9c4cd9949aecd4b7adf674a57e, for GNU/Linux 3.2.0, with debug_info, not stripped
~/bsidesnyc/includesecurity.com/caas  pwn checksec caas
[!] Could not populate PLT: No module named 'pkg_resources'
[*] '/home/victor/bsidesnyc/includesecurity.com/caas/caas'
    Arch:     i386-32-little
    RELRO:    Partial RELRO
    Stack:    Canary found
    NX:       NX disabled
    PIE:      No PIE (0x8048000)
    RWX:      Has RWX segments
```

We're dealing with a 32 bit executable that's dynamically linked (makes our lives easier) with the only security being a stack canary. This is important for our exploit which has a fun trick we'll need to employ to avoid overwriting the canary.

Here is the calculator function from our decompilation:

```
void calculator(void)

{
        int iVar1;
        int in_GS_OFFSET;
        byte *pbVar2;
        char *pcVar3;
        byte *pbVar4;
        byte local_38;
