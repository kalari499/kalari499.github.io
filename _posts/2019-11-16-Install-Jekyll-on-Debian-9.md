---
layout: post
title: Tips&#58; Installing Jekyll on Debian 9 family (Ruby 2.3 or before)
description: Workaround installation of Jekyll with Ruby version before 2.4.0, notably on Debian 9 and Devuan 2.
category: Tips
---

### Context

I recently faced a problem while migrating to Devuan 2 "ASCII".
You know what they say about stable distros: Its packages are old as heck.
On Debian 9 - Devuan 2, the Ruby package is version 2.3.3, which unfortunately [ends on 31/5/19](https://www.ruby-lang.org/en/news/2019/03/31/support-of-ruby-2-3-has-ended/).

Anyway, if you are installing Jekyll from upstream, version 4.0.0, you are likely to face a dependency issue with jekyll-sass-converter.
Something like this
```
jekyll-sass-converter requires Ruby version >= 2.4.0.
```
Took me quite a while to resolve, so I want to write this for those who face the same issue.

Also in case you don't know, Jekyll is used for generating static webpages, and it's used for GitHub pages, just like this very page you are reading.

This will be a short post about installing Jekyll from scratch on older stable distros with Ruby version 2.3.
I have tested on Debian 9 "Stretch" and Devuan 2 "ASCII" but this should work for others.

### Installation

If you read the official installation guide on Jekyll [here](https://jekyllrb.com/docs/installation/), you will see that Ruby version 2.4.0 is listed as requirements.
Again, this is due to jekyll-sass-converter, because sass gem has reached end-of-life.

However we can still do most of the step normally.

First creating local gem folder and add it to PATH:
```
export GEM_HOME="$HOME/.gems" >> ~/.bashrc
export PATH="$HOME/.gems/bin:$PATH" >> ~/.bashrc
source ~/.bashrc
```

Installing dependencies (from official doc):
```
sudo apt-get install ruby-full build-essential zlib1g-dev
```

Confirm version of ruby (this is the output of my Ruby version):
```
ruby --version
ruby 2.3.3p222 (2016-11-21) [x86_64-linux-gnu]
```

Installing bundler and jekyll, you shouldn't need to be root for this:
```
gem install bundler
gem install jekyll --version 3.0.0
```

Test to confirm jekyll has been install with:
```
bundle exec jekyll --version
```

If you see the output such as `jekyll 3.8.5` then you have successfully installed jekyll without dependency issues.

Now you can continue testing GitHub pages or whatever you are doing.
Small price to pay for stability I guess.

### The End

Don't forget to upgrade to higher version of Ruby (and Jekyll) soon.
Debian 10 "Buster" has also been released with Ruby 2.5.1 so you should be fine.
Not so much for Devuan 3 "Beowulf" that still has 2.3.3 Ruby (I think).

That's it for this post, see you next time.
