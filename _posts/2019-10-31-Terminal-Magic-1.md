---
layout: post
title: Terminal Magic&#58; Replace text recursively
description: How to replace all occurences of a word by another word in all files inside multiple directories.
category: Tips
---

### Context

Initially when I first created this blog, I made a folder named *resources/*, which was meant to contains all pictures and the like.
But now I want to change its name to be something different like *public/* or *assets/*.
The problem is that a lot of layouts and posts have referenced that folder, which means I have to find a way to change all of them as well.

![Screenshot](/assets/04-1.png)

### Avada Kedavra

Change to blog root directory and run
```bash
find . \( -type d -name .git -prune \) -o -type f -print0 | xargs -0 sed -i 's/\/resources/\/public/g'
```

And voila we are done.

### Explanation

- Part 1: *find*
    + `find .`: Find files under current directory
    + `\( -type d -name .git -prune \)`: Match all directories with name *.git* and do not descend to them
        * Avoid *.git* because directly editting files under it might corrupt git
        * Common technique when processing directory under *.git*
    + `-o -type f -print0`:  Or match all files and print their full filenames (separated by *\0*)
        * *print0* uses *\0* as delimiter, *print* uses newline as delimiter
        * Use *print0* to avoid weird cases when filenames contains blanks and newlines
    + ELI5: List all to-be-processed filenames

- Part 2: *xargs*
    + `-0`: Treat all as non-special, and use *\0* as delimiter
    + ELI5: Separate into multiple filenames and pass each to another command

- Part 3: *sed*
    + `-i`: Edit in-place (no backup)
    + `s/\/resources/\/public/g`: Change all */resources* to */public*
    + ELI5: Edit every occurence in a file

