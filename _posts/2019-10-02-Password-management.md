---
layout: post
title: Choosing a good passwords (part 1)
description: Part 1 of 2 posts about selecting strong passwords - How do hackers brute force ?
category: Security
---

## Introduction

We all know how important passwords are.
Just a string sequence away from accessing your bank account, your PayPal, your email,...

This means having a good and strong password is very crucial.
But unfortunately aside choosing password is largely user-driven.
Human are always the weakest chain in the security system.
People generally have very bad habit when it comes to password management.

- How many of you have used the same password for 1-2 years ?
- How many of you have used the same password for multiple sites ?
- How many of you have used stuff like "123456", "password", "qwerty',... into your password ?
- How many of you have put your name and/or birthday into your password ?
- ...

(Not so) fun fact, it is estimated that 4% of password is "123456".
But if you think you are better than that, how about this ?

- Using English word like monkey, dragon, football,...
- Using hunter2 instead of hunter
- Using p@ssw0rd instead of password
- ...

All of these things are not as safe as you think.
In this post, from the perspective of a script kiddie, I will explain how hackers/FBI/NSA crack your password, why there are some passwords that are weaker than others.

A note, if you aren't gonna read the post, at least check out [haveibeenpwned](https://haveibeenpwned.com/) to see if you are inside some data breaches or not.

## First, a bit of cryptography theory

We all agree that passwords are sensitive, so what companies can do to somewhat protect it from bad people ?
The answer is, nowadays all password are stored in [hashed form](https://en.wikipedia.org/wiki/Cryptographic_hash_function).

What it means is instead of saving your password as "password" (or so called plain-text), the company would save it as "5f4dcc3b5aa765d61d8327deb882cf99" (md5).
This hashing operation is also one-way, which means there is no way to look at that long random string and say the original password is "password".

This also explains why every site/service has "Reset your password" function instead of "We will send you your old password" function,
because literally the company themself don't know what the original password is.
If you see a site sending you your password then the company is storing your sensitive data in clear text.
In another word, run away, that's a timebomb waiting to explode.

Anyway, so you might ask, "Khoa, if the hashing operation is one-way, then how can hackers find my password ?"
Let's think for a bit, how can companies verify my password is correct ?
It's simple, companies just take your original password, hash it and compare.
2 different passwords are assumed to have different hashes, otherwise they are a collision.

A small note, a good hashing algorithm would have to make sure there are no collisions, or at least not easy to find collisions.
This is the reasons why SHA1 is considered to be insecure nowadays.

## How hackers/FBI/NSA crack your password ?

In order to defend yourself from the enemies, you need to understand how the enemy works first.
Generally there are 2 kinds of scenarios where your password might be in danger: targeted or mass-cracking
- Targeted is when a bad guy is aiming specificly for you, maybe personal issue or you are wanted by the FBI because you are Snowden/Hillary/...
- Mass-cracking is much more common, where the bad guy is aiming for low hanging fruit for quick benifit by trying easy common passwords. This also includes the scenario of a data breach.

Let's discuss mass-cracking situtation.

Data breaches happen everyday regardless of you might know or not.
The scale of a data breach can be something as small as local club to as big as [540 million users of Facebook](https://www.cbsnews.com/news/millions-facebook-user-records-exposed-amazon-cloud-server/).
For example this week we have DoorDash breach exposing 5 million users.
Normally breaches are bad publicity, so companies don't want it to appear too much on the news.
Sometimes they don't even disclose it.

What being leaked out from those breachs are of course users' data, password, personal information, bank account,...
Dumps from those breaches are being sold a lot on the black market for various price.

Let's hope you are not among the victims, but life is not always perfect right.

### Step 1: "I want to protect my account with 123456" - Common passwords

As we have discussed before, your password is in hashed form, or at least it should be.
Hackers couldn't find your orginal password but what they can do is taking a string, hash it and compare.
If they matches then voila, the string which they tried is your password, and you are in big trouble.
Otherwise they can just try another string.

Simple right ?
This technique is called "Bruteforce".
It is as old as tale, very simple yet effective.

Now the question is, how would hackers choose the string to try ?
There are a lot of exposed users to work with, and trying one by one for each of them is not a very smart thing to do.

If there are 1000 users having the same hashed, then cracking that hash would be more beneficial than cracking the lone hash of 1 random guy.
For common password like "password" or "123456", the number of users having that password is not 1000 anymore, but 100000s.
This is called "low-hanging fruits".

This brings me to my first point: __Stay away from common passwords__.

You can easily find list of common passwords on [Wikipedia](https://en.wikipedia.org/wiki/List_of_the_most_common_passwords) and [GitHub](https://github.com/danielmiessler/SecLists/tree/master/Passwords/Common-Credentials).
The most infamous wordlist that people used is `rockyou.txt`.
Basically RockYou stores 32 million users' passwords in plain-text and they got breached.
Never go full RockYou.

### Step 2: "I hate remember long passwords" - Short passwords

Next thing to do after common password, you go for short passwords.
Short passwords are also most of the time in the list of common passwords as well.

What hackers would do is trying password one by one.
For example of charset [A-Z] of capital letters, they would try A, B, C,..., Z then AA, AB, ...
Normal ASCII charset is 95 characters, including lowercase, uppercase, numbers and symbols.
However most of the times users go with just letters, so 52 characters.
This is literally try everything one-by-one until you find a hit.

But how short is "short password" ? And how long does it take to crack one ?

Obvious thing first, if your password is like 4-character long then your password would be cracked in like 1 second on a standard computer.
Never ever use that.

Minimum length of 8 is used to be the standard requirement everywhere. Let's do some math.
- If your password has length of 1, then they need to try at most 95 times to find your password
- For length of 2, this number goes up to 95 + 95\*95 = 9120 times
- For length of 8, this number is about 6\*10^15 times

But is it safe ?
6 quadrillion times, or 6 thousand billion times.
A standard computer can do about few billion calculation per second, and around 50 million password check per second.
This means it would takes like 1 year to crack that password.
I guess it is pretty strong right ?

Except it's not.
Hackers don't use 1 laptop to crack.
They use a lot of GPU power to crack, even in parallel.
It's reported in 2012 that [25-GPU cluster can try all 8-character passwords in 6 hours](https://arstechnica.com/information-technology/2012/12/25-gpu-cluster-cracks-every-standard-windows-password-in-6-hours/).
That was 7 years ago.
Yeah imagine nowadays GPU.
Remember Bitcoin mining power shit ? 

Even worse than that, hackers use their botnet to do the cracking as well.
A meidum botnet of 1000000 computers could crack that password in a few minutes.

Now if we raise the bar from 8 characters to 12 characters, the number of trial needed goes to 5\*10^23.
1 minute cracking now just turns into 100 million minutes, or somewhere around 200 years.
See how longer password can make a different ?
Numbers don't lie.

This brings me to my second point: __Use long password of length at least 12-14 characters, and it's preferable to use a mix of letters, numbers and symbols__.

### Step 3: "Human are predictable"

Most of the time results from previous part would give hackers enough goodies for further scamming, digging into other accounts and such.
Sometimes they don't even need to bruteforce for 8-character passwords (only 5-6).
Let's say they continue with the job for whatever reasons, what would they do next ?

All the calculations above just to show long password is strong in a mathematical way, or probablity way.
A random password generated by the computer would definitely have that level of strength.
But you are not a computer, and this is where "human are predictable" part comes into play.
There are many approaches for this in fact, and I will try to list a couple of easier approaches.
However most of these approaches don't give much good results when automated, so they are less important.

- Password reuse

How many different accounts do you have ? 20 ? 50 ? 100 ?
You don't have 50 different passwords for your 50 accounts don't you ?
Most people have at most 5-6 different passwords for their accounts a.k.a they reuse password a lot.

What if your Gmail account uses the same password with the forum account from 5 years ago that you don't even remember ?
The same forum just happened to be breached 2 years ago and its data in plain-text is on the black market ?

Exactly, hackers can use your email to search in previous breaches that you don't even know.
Check [haveibeenpwned](https://haveibeenpwned.com/) to see if your data has been leaked online before.

=> __Minimize sharing password between accounts, and change password often__.

- Personal information

Do you put your name (first name, family name,...) in your password ?
Do you put your birthday in your password ?

When it's come to long password, a lot of people make their password in the template of first name, first name + last name, first name + birthday, or similar.
Hackers can easily setup a quick rule to test these templates.

Commmon names appear on the top common passwords a lot, like ashley, bailey, charlie, donald,...

=> __Avoid setting passwords in predictable templates using personal information__.

- English words (dictionary words)

You know aside from "123456" or "qwerty", which passwords are also in the most common passwords ?
They are passwords like monkey, dragon, football, baseball, iloveyou, letmein, temp,...

People are far more likely to use dictionary words, and to some certain extends pronounceable words, as the password.
Also they tends to use more common words such as batman, superman or starwars instead of colonization or environmentalist.

- Obvious subtitutions

Sometimes a site wants you to put numbers and/or symbols into the passwords.
You would be surprise to know how often people try to do things like replacing o -> 0, e -> 3, l -> 1 or s -> $, a -> @.
Spoiler: It doesn't make the password more secure at all.

### Step 4: "I'm a high profile target"

If your password doesn't fall into any of the common pitfalls, then it is pretty strong for normal usage.
There are some other cases though, when either you are specific targeted by hackers, or you are a high profile targets of some state-level agency.
Or simpler, your hash ends up on DefCon anually password cracking competition.
Then in those cases, people will try harder to find your passwords, of course with more powers.

Still on the topic of human are predictable, most passwords are actually share the same general form: 1 root password + 1 appendage
- Root password can be a common password, a name (own name, parents, husband/wife, children,...), a dictionary word
- Appendage can be a single character (1, 2, x, !,...) or common prefix/suffix (abc, 4u, yt, fb,...)
- Some obvious subtitutions can also be tested, similar to previous part

There is a very cool paper from Carnegie Mellon University (10 authors) talking about the same topic [here](https://cups.cs.cmu.edu/rshay/pubs/Feedback.pdf)

In the case of state-level agency where they can get physical access to your device, they can also find all human-readable strings inside your drive.
So if you save your password unencrypted somewhere inside your computer then your password might be cracked that way as well.
Similar to data rescue, this sometimes would even work after you delete the files or such.

## Ending

That's it for this post.
I hope you enjoy the reading.

Next post, I will summarize every do's and don'ts from this post, give general guideline on setting strong passwords.
In addition, I will also talk about how to make the passwords memorable as well.

See you next time.
