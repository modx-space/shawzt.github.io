---
layout: post
title: How To Fix Raspbian "No Space Left" issue
description: Expand Raspbian Filesystem  
modified: 2017-01-26
tags: [Raspberry]
---

I would like to run some ruby programs on my little Raspberry Pi, so first of all, I need to install [Ruby][1] into my Raspberry Pi. However...

I use [rbenv][2] to manage ruby versions. As you know Raspberry Pi is not powerful enough, so it need more time to build and install ruby. The first time, after I waited for a while and I got an error that said "No Space Left On The Device".
Impossible, my microSD is 16GB, 😶  but the `df -h` shows something here:
![df-h](/assets/images/expand-filesystem-1.png)

After Google, I got the solution from raspberry forum, now I reposted it here:

+ Run command: `sudo raspi-config`    
+ You will see the following scree and select the `Expand Filesystem` option
  ![expand raspberry filesystem](/assets/images/expand-filesystem-2.png)
  ![expand raspberry filesystem](/assets/images/expand-filesystem-3.png)
+ Run command: `df -h`, you will see that the size of `/dev/root` is increased
  ![df-h](/assets/images/expand-filesystem-4.png)

[1]: https://www.ruby-lang.org/
[2]: http://rbenv.org/
