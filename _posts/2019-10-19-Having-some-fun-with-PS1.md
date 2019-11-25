---
layout: post
title: Tips&#58; Having some fun with PS1
description: Some fun things about PS1 variable of Linux shell
category: Tips
---

### What is PS1 ?

Do you remember your bash shell prompt that looks like this *[kalari499@My-Computer ~/some/kind/of/path] $* ?

Have you ever questioned what it is ? And why sometimes it is just *-bash-5.0$* ?

The answer is PS1.
In the man page of bash it says
```text
PS1     The value of this parameter is expanded (see PROMPTING below) and used as the primary prompt string.
        The default value is ``\s-\v\$ ''.
```

Note that it is the same thing for every POSIX shell out there: sh, chsh, zsh,...
Basically it's a builtin variable that would be printed out initially for every prompt.
Also checking man page of bash, section PROMPTING, also gives us a lot of backslash-escaped special characters, some common ones are:
- *\n*: Newline
- *\h*, *\H*: Hostname
- *\u*: Current user

See the man page for more.
The default value is *\s-\v\$*, which is just the name of the shell and the version.

Let's find some fun way to customize it.

Note that every command here is temporary on the current shell and not persistent.
If you want it to stay persistent, you need to put it into your *.bashrc*.


### Windows CMD

```bash
export PS1='C:${PWD////\\\\}>'
```

It would look like this on the computer, feel familiar now ?

![Screenshot](/assets/01-1.png)


### Let's do some coloring

```bash
export PS1="\[$(tput setaf 6)\][\u@\h \W]#\[$(tput sgr0)\] "
```

If your terminal supports coloring output, you could use *tput* to output color.
*tput setaf* for word color and *tput setbf* for background color.
You of course could also use ANSI coloring for it.
Here is my usual prompt

![Screenshot](/assets/01-2.png)


### Pranking your friend

```bash
export PS1="${PS1}sudo rm -rf /"
```

One of the only few times you can justified typing *sudo rm -rf /*.
If he doesn't know about PS1 he would be terrified.
Put it in .bashrc for maximum effect.

![Screenshot](/assets/01-3.png)

IMPORTANT: This is a dangerous command and you should never run this.
There might be a risk of accidentally executing it while typing or a backtick *`* eval.
Go for something tamer like *cd /* if you don't want to risk anything.


### Let's go apeshit like Parrot OS prompt

```bash
export PS1="\[\033[0;31m\]\342\224\214\342\224\200\$([[ \$? != 0 ]] && echo \"[\[\033[0;31m\]\342\234\227\[\033[0;37m\]]\342\224\200\")[$(if [[ ${EUID} == 0 ]]; then echo '\[\033[01;31m\]root\[\033[01;33m\]@\[\033[01;96m\]\h'; else echo '\[\033[0;39m\]\u\[\033[01;33m\]@\[\033[01;96m\]\h'; fi)\[\033[0;31m\]]\342\224\200[\[\033[0;32m\]\w\[\033[0;31m\]]\n\[\033[0;31m\]\342\224\224\342\224\200\342\224\200\342\225\274 \[\033[0m\]\[\e[01;33m\]\\$\[\e[0m\]"
```

Credit goes to a small blog but I couldn't remember.
You could try to translate it, I've given up trying to understand in detail.

Anyway it looks like this

![Screenshot](/assets/01-4.png)


### The End

That's it for this post.
Having customizing your prompt.

See you next time.
