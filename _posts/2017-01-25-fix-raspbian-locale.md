---
layout: post
title: How To Fix Raspbian Locale Issue
description: configure and fix Raspbian locale issue
modified: 2017-01-25
tags: [Raspberry]
---

I hope you have already setup your Raspberry Pi and made it running successfully. If not and you are also interested in this cool Pi, please review [how to active your Raspberry Pi][1].

For me, I have installed Raspberry Pi with official supported OS: [Raspbian][2], and it is running well, except throwing locale setting incorrect warnings. Here is the warning messages shown on my terminal once running any commands through SSH:

```
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_CTYPE = "zh_CN.UTF-8",
	LANG = "en_GB.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to a fallback locale ("en_GB.UTF-8").
```

##### Solution
And here is the solution to fix this annoying issue:

+ Run command: `sudo raspi-config`    
+ You will see the following screes and select the related option to configure your locale
  ![config locale](/assets/images/config-locale-1.png)
  ![change locale](/assets/images/config-locale-2.png)
  ![select locale](/assets/images/config-locale-3.png)
+ Edit the file `/etc/default/locale` and put the followings into it

```
LANG=en_US.UTF-8  
LC_ALL=en_US.UTF-8  
LC_CTYPE=en_US.UTF-8  
LANGUAGE=en_US.UTF-8
```

+ Restart your Raspberry Pi

[1]: /posts/setup-raspberry
[2]: https://www.raspberrypi.org/downloads/raspbian/
