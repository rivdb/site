---
layout: post
title:  "Sillygoose"
description: A simple binary search, number game
date:   2024-08-13
tags: ["Pwn", "Programming", "Binary Search"]
category: [CTF,N00bzCTF]
---

# Challenge Info
`There's no way you can guess my favorite number, you silly goose.`

attached: [sillygoose.py](/assets/files/sillygoose/sillygoose.py)

author: Connor Chang

![sillygoose](/assets/img/sillygoose/sillygoose.png)

## Breaking down the challenge

Upon connecting to the netcat listener, we're prompted to guess a number. Initially, I tried bruteforcing it for a few minutes, because why not? While it is possible for this challenge, it would take forever, so it's better if we analyze the sillygoose.py file that's attached with the challenge.

```py

from random import randint
import time
ans = randint(0, pow(10, 100))
start_time = int(time.time())
turns = 0
while True:
    turns += 1

    inp = input()

    if int(time.time()) > start_time + 60:
       print("you ran out of time you silly goose")
       break

    if "q" in inp:
        print("you are no fun you silly goose")
        break

    if not inp.isdigit():
        print("give me a number you silly goose")
        continue

    inp = int(inp)
    if inp > ans:
        print("your answer is too large you silly goose")
    elif inp < ans:
        print("your answer is too small you silly goose")
    else:
        print("congratulations you silly goose")
        f = open("/flag.txt", "r")
        print(f.read())

    if turns > 500:
        print("you have a skill issue you silly goose")
```

The rules are simple:

1. If the number is too large, we're prompted with `"your answer is too large you silly goose"`
2. If the number is too small, we're prompted with `"your number is too small you silly goose"`
3. If we run out of time (60 seconds), we're prompted with `"you run out of time you silly goose"`
4. If we guess over 500 times, we're prompted with `"you have a skill issue you silly goose"`

## The Solution

```py

from pwn import *
import time

# Establish connection
p = remote('24.199.110.35', 41199)
def get_response():
    try:
        response = p.recv().decode('utf-8').strip()
        print(f"Received response: {response}")  # Debugging line
        return response
    except EOFError:
        print("EOFError encountered")
        return ''

def send_message(message):
    p.sendline(message)

# Define the initial search range
low = 0
high = pow(10, 100)

# Record starting time
start_time = time.time()

# Initialize number of guesses (turns) made
turns = 0

while time.time() - start_time < 60:
    if turns > 500:
        print("Too many turns")
        break

    # Calculate guess
    guess = (low + high) // 2
    send_message(str(guess))
    response = get_response()
    turns += 1

    print(f"Guess: {guess}, Response: {response}")  # Debugging line

    if "congratulations" in response:
        print("Flag found!")
        print("Response content might contain the flag.")
        break
    elif "too large" in response:
        high = guess - 1
    elif "too small" in response:
        low = guess + 1
    elif "you have a skill issue" in response:
        print("Skill issue")
        break
    elif "you ran out of time" in response:
        print("Ran out of time")
        break
    elif "you are no fun" in response:
        print("No fun")
        break
    elif "give me a number" in response:
        print("Invalid input")

# Try to fetch more data if the flag might be hidden
try:
    additional_response = p.recvall().decode('utf-8').strip()
    print("Additional response:", additional_response)
except:
    print("Failed to fetch additional response")

# Close connection
p.close()
```


For each iteration, we halve the search space, which is what `guess = (low + high) // 2` does. This isn't necessary, but helps to optimize the runtime of the code.

If you want to better understand binary searches, [this](https://medium.com/@nicola.moro2312/simply-explained-binary-search-6aeb97690526) is a great article explaining it in better detail.

flag: `n00bz{y0u_4r3_4_sm4rt_51l1_g0053}`

