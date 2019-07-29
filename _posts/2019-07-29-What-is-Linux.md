---
layout: post
title: For BX2x&#58; What is Linux &#63;
category: BX2x
---

## Quick introduction to Linux

Everybody knows Windows. But not everybody knows what Linux is, which is totally understandable considering 88% of desktop/laptop is using Windows.
Chances are, you've never used a computer running a Linux distro before.

But that's gonna change now, because the school computers are running Linux. That would mean you have no choices but to learn how to use it, at least the basic. I hope this post would help you somewhat with getting used to the system.

So what is Linux ? To make it simple, Linux is a family of operating systems that using Linux kernel. The Linux kernel is free and open-source, meaning everybody can see the source code. The same kernel is also being used in Android system. A Linux distro, or distribution, is the Linux kernel bundle with a set of programs/tools. Most common Linux distros are either Debian-based (Debian, Ubuntu, Mint) or RPM-based (Red Hat, CentOS).

Generally Linux gives users more control over the system but much less user-friendly than Windows or MacOS. Also using it makes you look more geeky to other people not sure why. But an actual huge plus of using Linux is that it doesn't come with bloatwares. Some disadvantages of using Linux: no Words/Excel/PowerPoint (but you have free alternatives or online versions), no Photoshop/Lightroom, and most games are not compatible with Linux (so same problems with MacOS).

My advice (non-Mac) is keep using Windows for everyday usage, but if you are going to pursue ComSci major then you should consider getting used to Linux a bit more because the industry uses Linux heavily. Switching all the way to Linux would be difficult initially but you would learn a lot about C language, terminal and even its architecture (Semester 4 would be quite hell otherwise trust me).

Personally I use Ubuntu/Kali 95% of the times and Windows only for gaming with friends (yes you can dual boot or even triple/quadruple boot on your potato laptop, send me a message if you need help).

## How can I connect to school computers from my dorm room ?

School computers allow you to ssh remotely inside school network. You need your credentials and name of the computer (look at the sticker on the case).
- Linux or OSX: ssh is likely builtin. Open a terminal and type `ssh <username>@<computer>.polytechnique.fr` and type your password when being asked.
- Windows 10: Microsoft has OpenSSH enabled by default in April 2018. Open Powershell and do the same thing as Linux/OSX.
- Windows 8 or lower: You need to install PuTTY. Download it [here](https://www.chiark.greenend.org.uk/~sgtatham/putty/), find the appopriate 32-bit/64-bit version and please be careful checking the source before downloading. You really don't want virus inside the school network. After downloading, run putty.exe with hostname `<username>@<computer>.polytechnique.fr`, port 22 and type in your credentials when being asked.

You can connect to multiple computers or same computer multiple times. If I'm not wrong, then there is virtually no differences between computers, except when some are in Pale mode (no internet) once in a while, then you just need to choose a different computer in a different room.

## Basic Linux Commands

In Linux we do a lot of things inside the terminal, a.k.a the black screen with green text that you see hackers in a movie always look at. If you are in front of the computer itself, you might have minimal GUI like navigating around folders or some settings.

The equivalent of Windows `C:\` drive is `/` (which is the root). Linux is multi-user, the biggest boss (system administrator) is `root` and you are one of the least privilege user. If you think the computer as a dorm then each user would have his/her own room for you to put your personal stuffs inside, but unlikely that you could put your stuff inside your classmate's room or throw away stuff inside your teacher's room. Also \<directory\> means your current location, just like click to open a folder or back to previous folder and \<file\> is basically non-directory things.

With that being said, here are the most crucial commands for you to navigate around:
- `pwd`: Print your current directory
- `ls`: List all files/directories inside your current directory (`ls -la` for hidden files and more information)
- `ls <directory>`: List all files/directories inside the choosen directory (`ls -la` for hidden files and more information)
- `cd <directory>`: Open and change directory into the choosen one (If \<directory\> is not supplied then it would `cd` to your home folder at `~`)
- `cd ..`: Go back to parent folder
- `cat <file>`: Print the content of a file 
- `rm <file>`: Delete a file
- `mkdir <directory>`: Create a directory
- `rmdir <directory>`: Delete a directory
- `cp <from> <to>`: Copy file from location \<from\> to location \<to\> (`cp -r` to copy a folder) (Just like copy-paste)
- `mv <from> <to>`: Move directory/file from location \<from\> to location \<to\> (Just like cut-paste)
- `xdg-open <file>`: Open the file (Just like double click a file)
- `man <command>`: Show full manual of a command (usually very intensive)

Example of basic workflow, assume you just arrive and you want to organize by creating some folders
```
mkdir Semester1 (create folder Semester1)
cd Semester1 (go to Semester1)
mkdir MAA101 MAA201 (create folder MAA101 and MAA201, yes you can create multiple at the same time)
rmdir MAA201 (delete the folder)
```

## Other general useful Linux commands

This part is more for general use, consists of common basic commands you should know
- `nano/vim/emacs`: Text-editor, once you choose 1 thing you can never choose the others (vi/vim masterace for me)
- `whoami`: Pretty self-described. Print your username
- `echo <something>`: Print \<something\>
- `touch <file>`: Create empty file or update file if existed
- `file <file>`: Describe a file such as type, size,...
- `head/tail <file>`: Show the first/last 5 lines of file (flag `-n` for number of lines)
- `diff <file1> <file2>`: Find the differences between 2 files
- `wget`: Download a file from the internet

Unlikely that you would use inside the school computer but still common
- `history`: See your history of commands
- `w`: Show online users on the same computer
- `env`: Print all environment variables
- `ps`: See running processes (basically Task Manager in Windows)
- `wc -c/-l <file>`: Count number of characters/lines of a file

There are a lot more, but listing a lot of them here for you to remember definitely won't help. The best way to learn is to face the scenario where you need some specific commands with a little bit of Googling skill.

## Some random tricks

Not really advanced but I find myself using them a lot.
- Up/down arrow keys: Go backward/forward in history (really useful)
- Ctrl+C: Terminate a running program in the terminal foreground
- Task Manager equivalent: If you have a program hanging, say Firefox and you want to terminal it just like in Windows. The simplest and shortest solution is `kill -9 $(pgrep firefox)` (Not really recommended). Better solution:
```
Run ps -f -u $USER (show your current processes)
Look for the hanging process and its corresponding PID column, for example: 12345
Run kill -15 12345 (SIGTERM signal)
If doesn't work after a few seconds, run kill -2 12345 (SIGINT signal)
If still doesn't work, run kill -1 12345 (SIGHUP signal)
Last straw, run kill -9 12345 (SIGKILL signal)
```

## Ending

That's it for this post. Hope you learn something news from the post. Thanks for reading.
