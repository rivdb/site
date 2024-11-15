---
layout: post
title:  "Crackme-py"
description: A simple python challenge
date:   2024-11-02
tags: ["Medium", "Reverse Engineering", "Python", "Cryptography"]
category: [CTF,picoCTF]
---

## Challenge Info
[crackme.py](https://mercury.picoctf.net/static/f440bf2510a28914afae2947749f2db0/crackme.py)

The code for your convenience:
```py
# Hiding this really important number in an obscure piece of code is brilliant!
# AND it's encrypted!
# This want our biggest client to know his information is safe with us.
bezos_cc_secret = "A:4@r%uL`M-^M0c0AbcM-MFE0g4dd`_cgN"

# Reference alphabet
alphabet = "!\"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ"+ \
            "[\\]^_`abcdefghijklmnopqrstuvwxyz{|}~"



def decode_secret(secret):
    """ROT47 decode

    NOTE: encode and decode are the same operation in the ROT cipher family.
    """

    # Encryption key
    rotate_const = 47

    # Storage for decoded secret
    decoded = ""

    # decode loop
    for c in secret:
        index = alphabet.find(c)
        original_index = (index + rotate_const) % len(alphabet)
        decoded = decoded + alphabet[original_index]

    print(decoded)



def choose_greatest():
    """Echo the largest of the two numbers given by the user to the program

    Warning: this function was written quickly and needs proper error handling
    """

    user_value_1 = input("What's your first number? ")
    user_value_2 = input("What's your second number? ")
    greatest_value = user_value_1 # need a value to return if 1 & 2 are equal

    if user_value_1 > user_value_2:
        greatest_value = user_value_1
    elif user_value_1 < user_value_2:
        greatest_value = user_value_2

    print( "The number with largest positive magnitude is "
        + str(greatest_value) )

decode_secret(bezos_cc_secret)

choose_greatest()
```

## Understanding crackme.py
Let's break down step-by-step what each part of `crackme.py` does:

- Reference alphabet is defined:
  - A string called `alphabet` is defined, this includes special characters, numbers, and letters. This will be used as the foundation for encoding and decoding
- `decode_secret` function is defined:
  - The `decode_secret` function is made to decode a message, `bezos_cc_secret`, using ROT47
  - ROT47 works by shifting each character in the string by 47 positions (hence the 47) within the `alphabet` string
  - For each char in `bezos_cc_secret`, `decode_secret` will:
    - Find the char's position (index) in the `alphabet` string
    - Add 47 to the index, then wrap around the alphabet if needed, and find the new character
    - Append this new char to the decoded message
    - Then print the decoded message to reveal the hidden content of `bezos_cc_secret` (AKA our flag)
- `choose_greatest` function is defined:
  - Prompts the user to enter a first and a second number (`user_value_1` and `user_value_2`)
  - Start with the assumption that `user_value_1` is the greatest
  - Compare `user_value_1` and `user_value_2` *as strings*:
    - If `user_value_1` is larger, keep that as the largest
    - If `user_value_2` is larger, update `greatest_value` to `user_value_2`
  - Finally, print the greater number
- Run `choose_greatest`:
  - The script finally calls `choose_greatest`, thus prompting the user for input and then displaying the largest number based on string comparison

Let's give a quick summary, just to simplify:
- `decode_secret` is defined, it decodes `bezos_cc_secret`
- `choose_greatest` is defined to find and print the larger of two numbers entered by the user (us) is defined -
- **HOWEVER**... only `choose_greatest` is called

## The solution

So, the code already gives us our solution, but it isn't called. Instead, only `choose_greatest` is called. Why is this?

Simple, it's a **red herring**:
> Something that misleads or distracts from a relevant or important question

So, all we have to do is edit the python file to call `decode_secret`!

```py
# Hiding this really important number in an obscure piece of code is brilliant!
# AND it's encrypted!
# This want our biggest client to know his information is safe with us.
bezos_cc_secret = "A:4@r%uL`M-^M0c0AbcM-MFE0g4dd`_cgN"

# Reference alphabet
alphabet = "!\"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ"+ \
            "[\\]^_`abcdefghijklmnopqrstuvwxyz{|}~"



def decode_secret(secret):
    """ROT47 decode

    NOTE: encode and decode are the same operation in the ROT cipher family.
    """

    # Encryption key
    rotate_const = 47

    # Storage for decoded secret
    decoded = ""

    # decode loop
    for c in secret:
        index = alphabet.find(c)
        original_index = (index + rotate_const) % len(alphabet)
        decoded = decoded + alphabet[original_index]

    print(decoded)



def choose_greatest():
    """Echo the largest of the two numbers given by the user to the program

    Warning: this function was written quickly and needs proper error handling
    """

    user_value_1 = input("What's your first number? ")
    user_value_2 = input("What's your second number? ")
    greatest_value = user_value_1 # need a value to return if 1 & 2 are equal

    if user_value_1 > user_value_2:
        greatest_value = user_value_1
    elif user_value_1 < user_value_2:
        greatest_value = user_value_2

    print( "The number with largest positive magnitude is "
        + str(greatest_value) )

decode_secret(bezos_cc_secret) ### WHAT WE ADDED.

choose_greatest()

```

flag: `picoCTF{1|\/|_4_p34|\|ut_8c551048}`
