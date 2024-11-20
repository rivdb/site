---
layout: post
title:  "Weird Snake"
pin: True
description: Analyzing bytecode to reverse engineer an XOR key
date:   2024-11-02
tags: ["Medium", "Reverse Engineering", "Python", "Bytecode", "XOR"]
category: [CTF,picoCTF]
---
## Challenge Info

I have a friend that enjoys coding and he hasn't stopped talking about a snake recently He left this [file](https://artifacts.picoctf.net/c_titan/124/snake) on my computer and dares me to uncover a secret phrase from it. Can you assist?

The code for your convenience:

```bytecode

  1           0 LOAD_CONST               0 (4)
              2 LOAD_CONST               1 (54)
              4 LOAD_CONST               2 (41)
              6 LOAD_CONST               3 (0)
              8 LOAD_CONST               4 (112)
             10 LOAD_CONST               5 (32)
             12 LOAD_CONST               6 (25)
             14 LOAD_CONST               7 (49)
             16 LOAD_CONST               8 (33)
             18 LOAD_CONST               9 (3)
             20 LOAD_CONST               3 (0)
             22 LOAD_CONST               3 (0)
             24 LOAD_CONST              10 (57)
             26 LOAD_CONST               5 (32)
             28 LOAD_CONST              11 (108)
             30 LOAD_CONST              12 (23)
             32 LOAD_CONST              13 (48)
             34 LOAD_CONST               0 (4)
             36 LOAD_CONST              14 (9)
             38 LOAD_CONST              15 (70)
             40 LOAD_CONST              16 (7)
             42 LOAD_CONST              17 (110)
             44 LOAD_CONST              18 (36)
             46 LOAD_CONST              19 (8)
             48 LOAD_CONST              11 (108)
             50 LOAD_CONST              16 (7)
             52 LOAD_CONST               7 (49)
             54 LOAD_CONST              20 (10)
             56 LOAD_CONST               0 (4)
             58 LOAD_CONST              21 (86)
             60 LOAD_CONST              22 (43)
             62 LOAD_CONST              23 (106)
             64 LOAD_CONST              24 (123)
             66 LOAD_CONST              25 (89)
             68 LOAD_CONST              26 (87)
             70 LOAD_CONST              27 (18)
             72 LOAD_CONST              28 (62)
             74 LOAD_CONST              29 (47)
             76 LOAD_CONST              20 (10)
             78 LOAD_CONST              30 (78)
             80 BUILD_LIST              40
             82 STORE_NAME               0 (input_list)

  2          84 LOAD_CONST              31 ('J')
             86 STORE_NAME               1 (key_str)

  3          88 LOAD_CONST              32 ('_')
             90 LOAD_NAME                1 (key_str)
             92 BINARY_ADD
             94 STORE_NAME               1 (key_str)

  4          96 LOAD_NAME                1 (key_str)
             98 LOAD_CONST              33 ('o')
            100 BINARY_ADD
            102 STORE_NAME               1 (key_str)

  5         104 LOAD_NAME                1 (key_str)
            106 LOAD_CONST              34 ('3')
            108 BINARY_ADD
            110 STORE_NAME               1 (key_str)

  6         112 LOAD_CONST              35 ('t')
            114 LOAD_NAME                1 (key_str)
            116 BINARY_ADD
            118 STORE_NAME               1 (key_str)

  9         120 LOAD_CONST              36 (<code object <listcomp> at 0x7f704e8a4d40, file "snake.py", line 9>)
            122 LOAD_CONST              37 ('<listcomp>')
            124 MAKE_FUNCTION            0
            126 LOAD_NAME                1 (key_str)
            128 GET_ITER
            130 CALL_FUNCTION            1
            132 STORE_NAME               2 (key_list)

 11     >>  134 LOAD_NAME                3 (len)
            136 LOAD_NAME                2 (key_list)
            138 CALL_FUNCTION            1
            140 LOAD_NAME                3 (len)
            142 LOAD_NAME                0 (input_list)
            144 CALL_FUNCTION            1
            146 COMPARE_OP               0 (<)
            148 POP_JUMP_IF_FALSE      162

 12         150 LOAD_NAME                2 (key_list)
            152 LOAD_METHOD              4 (extend)
            154 LOAD_NAME                2 (key_list)
            156 CALL_METHOD              1
            158 POP_TOP
            160 JUMP_ABSOLUTE          134

 15     >>  162 LOAD_CONST              38 (<code object <listcomp> at 0x7f704e8a4df0, file "snake.py", line 15>)
            164 LOAD_CONST              37 ('<listcomp>')
            166 MAKE_FUNCTION            0
            168 LOAD_NAME                5 (zip)
            170 LOAD_NAME                0 (input_list)
            172 LOAD_NAME                2 (key_list)
            174 CALL_FUNCTION            2
            176 GET_ITER
            178 CALL_FUNCTION            1
            180 STORE_NAME               6 (result)

 18         182 LOAD_CONST              39 ('')
            184 LOAD_METHOD              7 (join)
            186 LOAD_NAME                8 (map)
            188 LOAD_NAME                9 (chr)
            190 LOAD_NAME                6 (result)
            192 CALL_FUNCTION            2
            194 CALL_METHOD              1
            196 STORE_NAME              10 (result_text)
            198 LOAD_CONST              40 (None)
            200 RETURN_VALUE

Disassembly of <code object <listcomp> at 0x7f704e8a4d40, file "snake.py", line 9>:
  9           0 BUILD_LIST               0
              2 LOAD_FAST                0 (.0)
        >>    4 FOR_ITER                12 (to 18)
              6 STORE_FAST               1 (char)
              8 LOAD_GLOBAL              0 (ord)
             10 LOAD_FAST                1 (char)
             12 CALL_FUNCTION            1
             14 LIST_APPEND              2
             16 JUMP_ABSOLUTE            4
        >>   18 RETURN_VALUE

Disassembly of <code object <listcomp> at 0x7f704e8a4df0, file "snake.py", line 15>:
 15           0 BUILD_LIST               0
              2 LOAD_FAST                0 (.0)
        >>    4 FOR_ITER                16 (to 22)
              6 UNPACK_SEQUENCE          2
              8 STORE_FAST               1 (a)
             10 STORE_FAST               2 (b)
             12 LOAD_FAST                1 (a)
             14 LOAD_FAST                2 (b)
             16 BINARY_XOR
             18 LIST_APPEND              2
             20 JUMP_ABSOLUTE            4
        >>   22 RETURN_VALUE
```

## Defining constants, loading values into `input_list`
```bytecode
  1           0 LOAD_CONST               0 (4)
              2 LOAD_CONST               1 (54)
              4 LOAD_CONST               2 (41)
             80 BUILD_LIST              40
             82 STORE_NAME               0 (input_list)
```
- `LOAD_CONST` is called to loads several constant values onto the stack.
  - 4, 54, 41, etc. are being loaded.
- `BUILD_LIST` is called, it combines the constants that were loaded into a single list. The number **40** in `BUILD_LIST 40` tells the (Python) interpret that the list will contain 40 values.
- `STORE_NAME` assigns the list to a variable named "input_list"

This may seem complicated, but the Python code for this looks something like:

```py
input_list = [4, 54, 41, 0, 112, 32, 25, 49, 33, 3, 0, 0, 57, 32, ...]
```
## Initializing `key_str`


```bytecode
84 LOAD_CONST              31 ('J')
86 STORE_NAME               1 (key_str)

88 LOAD_CONST              32 ('_')
90 LOAD_NAME                1 (key_str)
92 BINARY_ADD
94 STORE_NAME               1 (key_str)

96 LOAD_NAME                1 (key_str)
98 LOAD_CONST              33 ('o')
100 BINARY_ADD
102 STORE_NAME               1 (key_str)

104 LOAD_NAME                1 (key_str)
106 LOAD_CONST              34 ('3')
108 BINARY_ADD
110 STORE_NAME               1 (key_str)

112 LOAD_CONST              35 ('t')
114 LOAD_NAME                1 (key_str)
116 BINARY_ADD
118 STORE_NAME               1 (key_str)

```
As we can see, many values are loaded onto a "key" `variable`. If we read it in order, it might seem as though the key is `J_o3t`. **This is not the case.**

> Read carefully, as this was designed to confuse you
{: .prompt-info}

1.
- `LOAD_CONST ('J')`: This loads a constant "J" onto the stack.
- `STORE_NAME (key_str)`: The value at the top of the stack ("J", in this instance) is popped off the stack and assigned to the variable `key_str`.
  - `key_str = "J"`.


2.
- `LOAD_CONST ('_')`: This loads a constant "\_" onto the stack.
- `LOAD_NAME (key_str)`: This loads `key_str`, which is currently just "J", as previously explained.
- `BINARY_ADD`: Combines both `_` and `key_str` (again, currently just "J").
- `STORE_NAME (key_str)`: Stores these changes to `key_str`.
  - `key_str = "J"` --> `key_str = "_J"`.

3.
- `LOAD_NAME (key_str)`: This loads `key_str`.
- `LOAD_CONST ('o')`: This loads a constant "o" onto the stack.
- `BINARY_ADD`: Combines both "O" and `key-str`, effectively adding "" to `key_str`.
- `STORE_NAME (key_str)`: Stores these changes to `key_str`.
  - `key_str = "_J"` --> `key_str = "_Jo"`.

4.
- `LOAD_CONST ('3')`: This loads a constant "3" onto the stack.
- `LOAD_NAME (key_str)` This loads `key_str`.
- `BINARY_ADD`: This combine "3" and `key_str`.
- `STORE_NAME (key_str)`: Stores this to `key-str`.
  - `key_str = "_Jo"` --> `key_str = "_Jo3"`.

5.
- `LOAD_CONST ('t')`: This loads a constant "t" onto the stack.
- `LOAD_NAME (key_str)`: This loads `key_str`.
- `BINARY_ADD`: This combine "t" and `key-str`.
- `STORE_NAME (key_str)`.
  - `key-str = "_Jo3"` --> `key_str = "_Jo3t"`.

So, now you can see our final key is actually `_Jo3t`, rather than `J_o3t`.

## Generating `key_list`
```bytecode
120 LOAD_CONST              36 (<code object <listcomp> at 0x7f704e8a4d40, file "snake.py", line 9>) >)
122 LOAD_CONST              37 ('<listcomp>')
124 MAKE_FUNCTION            0
126 LOAD_NAME                1 (key_str)
128 GET_ITER
130 CALL_FUNCTION            1
132 STORE_NAME               2 (key_list)
```
- `120 LOAD_C NST 36(<code object <listcomp> at ..., file "snake.py", line 9>) >)`: This loads a constant at index 36 onto the stack, specifically, a code object for a [list comprehension](https://www.simplilearn.com/tutorials/python-tutorial/list-comprehension-in-python).
  - Essentially, a piece of code that will create a list. This code object will be used to generate the list later.
- `122 LOAD_CONST 37 ('<listcomp>')`: This loads a constant at index 37 onto the stack. The constant in this case is the string "\<listcomp>". This will be used to identify this code object as a list comprehension once it's actually executed.
- `124 MAKE_FUNCTION 0`: This is the simplest one, it simply creates a function from the code object and the string "\<listcomp>" (which will be used for debugging and identification). When this new function object is called, it'll execute the list comprehension code.
- `126 LOAD_NAME 1 (key_str)`: Loads the `key_str` variable that was explained in the previous section
- `128 GET_ITER`: Takes the value of `key_str`, which was just loaded, and gets an iterator over it. Assuming that `key_str` is a string, it'll iterate over all its characters.
- `130 CALL_FUNCTION 1`: Calls the function was made in step 124, it passes the iterator from `key_str` as an argument to the list comprehension. The function runs the list comprehension, which will process the characters from `key_str`.
- `132 STORE_NAME 2 (key_list)`: The result of the list that was just made is stored in a variable named `key_list`. This is simply the final output of the list comprehension.

In python, this might look something like this:

```py
key_list = [ord(item) for item in key]
```

This is a simple list comprehension, where each elemtn in the list is an item/character from `key_str`.

## Extending `key_list` if it's shorter than `input_list`

```bytecode
134 LOAD_NAME                3 (len)
136 LOAD_NAME                2 (key_list)
138 CALL_FUNCTION            1
140 LOAD_NAME                3 (len)
142 LOAD_NAME                0 (input_list)
144 CALL_FUNCTION            1
146 COMPARE_OP               0 (<)
148 POP_JUMP_IF_FALSE      162

150 LOAD_NAME                2 (key_list)
152 LOAD_METHOD              4 (extend)
154 LOAD_NAME                2 (key_list)
156 CALL_METHOD              1
158 POP_TOP
160 JUMP_ABSOLUTE          134
```
**The first half (before the line break):**
- Check lengths:
  - `len(key_list)` is compared to `len(input_list)`.
  - If `key_list` is shorter, then it is extended.
  - Otherwise, it just skips to the next part of the code.

**The second half (after the line break)**
- Extend `key_list`:
  - `key_list.extend(key_list)` duplicates `key_list` by adding its contents to itself.
  - After extending, the program jumps back (`JUMP_ABSOLUTE 134`) to recheck the lengths.
  - The program will continue to loop until `len(key_list)` is >= `len_(input_list)`.

An example of how this might work:

- Initial Values:

```py
key_list = [1, 2, 3]
input_list = [10, 20, 30, 40, 50]
```

- After extending once:

```py
key_list = [1, 2, 3, 1, 2, 3]
```

## XOR operation between `input_list` and `key_list`
```bytecode
162 LOAD_CONST              38 (<code object <listcomp> at ...>)
164 LOAD_CONST              37 ('<listcomp>')
166 MAKE_FUNCTION            0
168 LOAD_NAME                5 (zip)
170 LOAD_NAME                0 (input_list)
172 LOAD_NAME                2 (key_list)
174 CALL_FUNCTION            2
176 GET_ITER
178 CALL_FUNCTION            1
180 STORE_NAME               6 (result)
```

- Offsets `162 - 166`:
  - A code object for a list comprehension is loaded onto the stack.
  - A name label for the list comprehension (`<listcomp>`) is loaded, which is used solely for debugging purposes, like I mentioned previously.
  - The compiled code and its label are combined into a callable function that will execute the list comprehension once it's called.
  - This list comprehension would look something like this:
```py
result = [a ^ b for a, b in zip(input_list, key_list)]
```
- Offsets `168`:
  - The built-in `zip` function is loaded onto the stack.
  - This function will combine `input_list` and `key_list` into pairs.

- Offsets `170 - 172`:
  - The variables `input_list` and `key_list` are loaded onto the stack.
  - Example:
    - `input_list = [4, 54, 41]`, `key_list = [116, 95, 74]`

> Note that these aren't the actual values, I'm just using them as an example.
{: .prompt-info }

- Offsets `174 - 180`:
  - The `zip` function is called with 2 arguments: `input_list` and `key_list`. The result is an iterator of pairs like: `[(4, 116), (54, 95), (41, 74)]`.
  - `CALL_FUNCTION (1)`: the function created for the list comprehension is called with the iterator of pairs.
  - `STORE_NAME (result)`: The list that was just created from the list comprehension is stored in the variable `result`.

So, the list comprehension should:
- Iterate through each pair (a, b) from the zipped object.
- Compute the XOR (a^b).
- Add these results to the new list called `result`

## Converting result into text
```bytecode
182 LOAD_CONST              39 ('')
184 LOAD_METHOD              7 (join)
186 LOAD_NAME                8 (map)
188 LOAD_NAME                9 (chr)
190 LOAD_NAME                6 (result)
192 CALL_FUNCTION            2
194 CALL_METHOD              1
196 STORE_NAME              10 (result_text)
```
- `result` (which is a list of integers) is converted into characters using `chr()`.
- These characters are then joined into a single string using `''.join()`.
- The final output is stored into `result_text` (our flag!).

## Solution

```python
input_list = []

with open("snake", "r") as file:
    for line in file:
        start = line.find("(")
        end = line.find(")")
        if start != -1 and end != -1:
            try:
                number = int(line[start + 1:end])
                input_list.append(number)
            except ValueError:
                continue

key = "t_Jo3"

key_list = [ord(item) for item in key]

while len(key_list) < len(input_list):
    key_list.extend(key_list[:len(input_list) - len(key_list)])

result = [a ^ b for a, b in zip(input_list, key_list)]

result_text = ''.join(map(chr, result))

print(result_text)
```

> Note: The `with` statement before the `key` is to automatically find the values of `input_list`. This isn't necessary of course, it's just to automate this further.
{: .prompt-info }

flag: `picoCTF{N0t_sO_coNfus1ng_sn@ke_516dfaee}`
