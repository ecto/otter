one time pad toy

![otter](https://i.imgur.com/RTw97Kq.png)

## intro

I decided I wanted to try my hand at lisp. I couldn't think of a good first project, but I remembered something from my childhood. When I was a kid, I "helped" my dad write a chapter in a C++ book, meaning I mostly sat on his lap and asked dumb questions. Our chapter was on crypto, and he wrote a [one-time pad](https://en.wikipedia.org/wiki/One-time_pad) program for one of the examples. I figured it would be fun to do it on my own! So.. that's why this exists. You probably shouldn't use it for anything important.

## install

Aparently there are a million different lisp implementations (I guess polish notation is easy to write interpreters for?). I use OSX and I chose `clisp` because it was the first in a `brew search lisp`. So on OSX:

````bash
brew install clisp
````

Download my code:

````
git clone https://github.com/ecto/otter.git
cd otter
````

and run it:

````
./otter

Usage: otter [command]

Commands:
  help                           display this message
  gen [bytes=4096]               generate [bytes] of random data
  enc [input] [pad] [offset=0]   encrypt [input] file with [pad] file starting at [offset] bytes of [pad] file
  dec [input] [pad] [offset=0]   decrypt [input] file with [pad] file starting at [offset] bytes of [pad] file
````

If you want, you can symlink it:

````bash
ln -s $(echo `pwd`/otter) /usr/local/bin/otter
````

## use

Create a file you'd like to encrypt:

````bash
echo "hello, world" > in
cat in
# hello, world
````

Create a pad:

````bash
otter gen > pad
````

**Send the pad to your friend on a secure medium (not the Internet)**

Encrypt the file:

````bash
otter enc in pad > out
cat out
# L\}hd2><`
# yours will be different, because the pad is different
````

**Send the file to your friend however you want!**

Decrypt the file:

````bash
otter dec out pad > message
cat message
# hello, world
````

## considerations

The security of one time pads relies on the key material not being used more than once, and that it remains secret (hopefully this is obvious). A common OTP practice is to generate a very large pad, and share it ahead of time.

If you'd like to generate a 10 megabyte pad, you can do so by adding the pad size to the `gen` command:

````bash
otter gen 10000000 > pad
````

When you encrypt or decrypt something, keep a running count of the bytes you've traversed, and pass them to the `enc`/`dec` commands.

For example, we can find the size of the message we encrypted earlier:

````bash
ls -l in
-rw-r--r--  1 ecto  staff  13 Apr  4 18:51 in
````

So we've used 13 bytes of the pad. The next time we want to encrypt something, we use 13 for the offset:

````bash
echo "hope all is well" > second-in
otter enc second-in pad 13 > second-out
````

Now when our friend decrypts the message, they will also need to specify the pad output:

````bash
otter dec second-out pad 13 > second-message
````

## license

````
        DO WHAT THE FUCK YOU WANT TO PUBLIC LICENSE
                    Version 2, December 2004

 Copyright (C) 2004 Sam Hocevar <sam@hocevar.net>

 Everyone is permitted to copy and distribute verbatim or modified
 copies of this license document, and changing it is allowed as long
 as the name is changed.

            DO WHAT THE FUCK YOU WANT TO PUBLIC LICENSE
   TERMS AND CONDITIONS FOR COPYING, DISTRIBUTION AND MODIFICATION

  0. You just DO WHAT THE FUCK YOU WANT TO.
````
